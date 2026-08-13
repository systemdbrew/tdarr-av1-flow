# Changelog

All notable changes to this project will be documented here.

## [Unreleased]

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
