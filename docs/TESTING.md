# Testing checklist

Do not enable this flow against an irreplaceable library until representative samples have passed these checks.

## Test matrix

### 1. 1080p Blu-ray REMUX

Confirm:
- AV1 QSV is selected;
- output is 10-bit AV1;
- selected Q tier matches source video bitrate;
- audio is stream-copied;
- output duration is within tolerance;
- output is smaller than source.

### 2. 2160p HDR10 REMUX

Confirm:
- HDR metadata remains usable after AV1 conversion;
- no unintended SDR tone-map occurs;
- pixel format is 10-bit;
- playback works in the intended Jellyfin clients.

### 3. Dolby Vision REMUX

Confirm:
- DV is detected;
- video is not AV1-transcoded;
- the resulting MKV still reports expected Dolby Vision metadata/profile;
- audio/subtitle cleanup remains correct.

### 4. Foreign-language Movie with English dub

Confirm:
- original language is resolved correctly;
- best original-language track is retained;
- best English track is retained;
- unwanted language tracks are removed;
- commentary does not win over the main track.

### 5. TV episode with foreign-language/original audio

Confirm Sonarr/TMDB original-language lookup and audio selection.

### 6. Forced subtitles

Use a title with foreign dialogue requiring forced subtitles.

Confirm:
- English subtitle streams remain;
- forced streams remain even when not tagged English;
- unrelated non-English, non-forced subtitles are removed.

### 7. Already-AV1 file

Confirm:
- video is not re-encoded;
- stream cleanup/container processing still works when needed;
- safety checks do not cause unnecessary destructive replacement.

## Useful verification commands

Examples only; adjust paths for your environment.

```bash
ffprobe -v error -show_streams -show_format "file.mkv"
```

For a compact stream list:

```bash
ffprobe -v error \
  -show_entries stream=index,codec_type,codec_name,profile,pix_fmt,channels,bit_rate:stream_tags=language,title:stream_disposition=default,forced \
  -of json "file.mkv"
```

## What to include in a bug report

- Tdarr version
- Tdarr Node version
- Intel GPU model
- FFmpeg version
- source resolution / codec / HDR or DV status
- source video bitrate
- Q tier selected by the flow
- original file size
- output file size
- expected vs actual audio streams
- expected vs actual subtitle streams
- relevant Tdarr job log
