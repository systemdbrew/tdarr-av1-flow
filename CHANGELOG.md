# Changelog

All notable changes to this project will be documented here.

## [Unreleased]

## [0.1.2] - 2026-08-12

### Fixed
- Explicitly initializes QSV hardware frames on every adaptive-quality branch with `-hwaccel qsv -hwaccel_output_format qsv`.
- Keeps the 10-bit AV1 path on QSV hardware surfaces so `scale_qsv=format=p010le` receives compatible input frames.
- Disables the encoder node's implicit hardware-decoding injection and makes the QSV input pipeline explicit and reproducible in the generated FFmpeg command.

### Verified in v0.1.1 testing
- The previous invalid AV1 `main10` profile issue is fixed; FFmpeg now uses AV1 Main profile.
- Radarr original-language detection, audio stream-copy cleanup, and English subtitle selection continue to behave correctly.
- The v0.1.1 failure was isolated to software frames being passed into `scale_qsv`, before source replacement.

### Status
- Pre-release bugfix; rerun the same 2 Fast 2 Furious REMUX regression test before broad library use.

## [0.1.1] - 2026-08-12

### Fixed
- Replaced Tdarr's generic `10 Bit Video` node for the QSV AV1 path because it emits `-profile:v main10`, which `av1_qsv` rejects.
- AV1 QSV 10-bit output now uses AV1 Main profile with QSV P010 surfaces via `scale_qsv=format=p010le`.
- Enabled hardware decoding through the QSV encoder node so Tdarr supplies the correct QSV decode arguments instead of relying on the deprecated implicit `-hwaccel qsv` behavior.
- Removed duplicate manual QSV input arguments from each adaptive-quality branch.

### Verified in v0.1.0 testing
- Radarr original-language lookup correctly identified English for the test REMUX.
- Audio cleanup kept the best English/original DTS:X 7.1 track and removed the English commentary track without transcoding audio.
- Subtitle cleanup selected English subtitle streams while removing unwanted languages.
- The first encode failure was isolated to the invalid `main10` AV1 QSV profile and did not modify the source file.

### Status
- Pre-release bugfix; repeat the same regression test before broad library use.

## [0.1.0] - 2026-08-12

### Added
- Initial universal Movie + TV Tdarr AV1 QSV flow.
- Visually explicit source bitrate decision tree with Q20/Q21/Q22/Q23 branches.
- Dedicated visible policy nodes for original-language detection, audio cleanup, subtitle cleanup, and Dolby Vision detection.
- 10-bit AV1 QSV encoding.
- Original-language + English audio selection with stream-copy preservation.
- English + forced subtitle retention.
- Conservative Dolby Vision bypass.
- Q20 fallback when MediaInfo does not expose a usable video bitrate.
- MKV output standardization.
- Duration, health, and size validation before replacement.
- Radarr and Sonarr refresh nodes.
- Optional Discord/Apprise notification node.
- Setup, testing, and automated release documentation.

### Status
- Initial pre-release intended for controlled validation before broad production use.
