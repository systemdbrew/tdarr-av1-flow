# Tdarr AV1 QSV Flow

A high-quality Tdarr Flow for Intel Arc / Quick Sync Video (QSV) users who want to convert REMUX-quality Movies and TV to AV1 while preserving the streams that matter.

> Status: **early testing / v0.1.x quality**. Test against copies of representative media before enabling it on an entire library.

## Install

The recommended install path is the latest GitHub Release:

1. Open **Releases**.
2. Download the versioned JSON asset, for example `tdarr-av1-flow-v0.1.0.json`.
3. Import that JSON into Tdarr under **Flows**.

Release assets are generated automatically from the exact tagged repository tree and include a SHA-256 checksum in the release notes.

The development copy is also kept in:

`flows/Darkroast_Lightroast_Master_AV1_QSV.json`

## Goals

- Convert non-AV1 video to **10-bit AV1 using Intel QSV**.
- Use a single flow for both Movies and TV.
- Adapt AV1 quality to source video bitrate rather than assuming all Movies or all TV should use the same quality.
- Keep the **best original-language audio** and the **best English audio** without transcoding audio.
- Keep **English subtitles** and **forced subtitles**.
- Preserve Dolby Vision sources by skipping the AV1 video transcode when DV is detected.
- Standardize output to **MKV**.
- Validate output duration, run a thorough FFmpeg health check, and reject outputs that are not smaller than the source.
- Refresh **Radarr and Sonarr** after replacement.
- Optionally send a Discord notification through Tdarr's Apprise Flow plugin.

## Adaptive quality policy

| Source video bitrate | QSV AV1 quality |
| --- | ---: |
| >= 20 Mbps | Q20 |
| 10-20 Mbps | Q21 |
| 5-10 Mbps | Q22 |
| < 5 Mbps | Q23 |

Lower Q values mean higher quality. The fallback is Q20 when a usable source bitrate cannot be determined, which is intentionally conservative for REMUX-oriented libraries.

## Audio policy

The flow attempts to determine the title's original language using Radarr first, then Sonarr + TMDB when needed.

It then keeps:

- the highest-ranked main audio stream in the original language;
- the highest-ranked main English audio stream;
- a safe fallback audio stream when metadata is incomplete.

Audio is **stream-copied**, not transcoded. Ranking favors non-commentary tracks, lossless/high-quality codecs, channel count, and bitrate.

If the original language cannot be determined safely, the flow keeps all audio rather than making a destructive guess.

## Subtitle policy

The flow keeps subtitle streams when either condition is true:

- language is English;
- stream is marked forced, or its title indicates it is forced.

Other subtitle streams are removed.

## Dolby Vision

Dolby Vision is treated conservatively. When Tdarr/FFprobe exposes DV/DOVI metadata, the flow skips the AV1 video encode and preserves the video stream rather than silently discarding DV metadata.

HDR10/non-DV sources continue through the 10-bit AV1 QSV path.

## Safety checks

Before replacing the library file, the flow:

1. requires the new duration to be within **99.5%-100.5%** of the original;
2. runs a **thorough FFmpeg health check** using QSV acceleration;
3. requires the working file to be **smaller than the original**;
4. only then replaces the original file.

## Required Tdarr global variables

Create these global variables in Tdarr:

| Variable | Purpose |
| --- | --- |
| `radarr_url` | Radarr base URL, e.g. `http://radarr:7878` |
| `radarr_api_key` | Radarr API key |
| `sonarr_url` | Sonarr base URL, e.g. `http://sonarr:8989` |
| `sonarr_api_key` | Sonarr API key |
| `tmdb_api_key` | TMDB API v3 key used for TV original-language lookup |
| `discord_webhook` | Optional Discord webhook value for Apprise |

The exported flow contains no real credentials.

## Discord notification

An Apprise notification node is included but disabled by default. Enable it only after confirming `apprise` is available inside the Tdarr worker/container and after configuring `discord_webhook`.

## Recommended validation before production

Test representative copies of:

- 1080p Blu-ray REMUX;
- 2160p HDR10 REMUX;
- Dolby Vision REMUX;
- foreign-language title with an English dub;
- title containing English forced subtitles;
- title already encoded as AV1;
- TV episode and Movie with similarly high production quality.

Verify video codec/pixel format, HDR/DV behavior, retained audio, retained subtitles, duration, final file size, and Radarr/Sonarr refresh behavior before processing a full library.

See [`docs/TESTING.md`](docs/TESTING.md) for the validation matrix and [`docs/RELEASING.md`](docs/RELEASING.md) for the automated release process.

## Hardware

Designed around Intel hardware with FFmpeg `av1_qsv` support, such as modern Intel Arc GPUs.

If `av1_qsv` is unavailable, the flow fails rather than silently falling back to NVENC or CPU encoding.

## Why one flow for Movies and TV?

Runtime and library type are poor indicators of source quality. A high-end one-hour TV episode may deserve the same treatment as a feature film. This flow therefore uses characteristics of the media itself, particularly source video bitrate, to choose its AV1 quality target.

## Contributing

Issues and test results are welcome. Particularly useful reports include source codec, resolution, HDR/DV status, source video bitrate, selected Q value, original/final size, retained streams, and relevant Tdarr job-log excerpts.

## Disclaimer

This project is an independent community configuration for Tdarr and is not affiliated with Tdarr, Intel, Radarr, Sonarr, or TMDB. Back up important media and validate the flow against your own environment before bulk processing.
