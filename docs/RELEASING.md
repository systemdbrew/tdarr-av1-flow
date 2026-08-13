# Releasing

Releases are created by the **Create Release** GitHub Actions workflow.

## Normal release

1. Make sure `main` contains the exact flow and documentation you want to publish.
2. Update `CHANGELOG.md` with the release version, for example `## [0.2.0] - YYYY-MM-DD`.
3. Open **Actions → Create Release → Run workflow**.
4. Enter a version such as `v0.2.0`, a title, and choose whether it is a pre-release.
5. Run the workflow.

The workflow validates the JSON, creates the Git tag when needed, copies the canonical flow to a versioned asset such as `tdarr-av1-flow-v0.2.0.json`, calculates its SHA-256 checksum, reads the matching changelog section, and publishes the GitHub Release.

## Existing tags

Release tags are treated as immutable. If the requested tag already exists but points at a different commit, the workflow stops instead of silently moving the tag.

For an unpublished/testing tag that you intentionally want to replace, delete both the old GitHub Release **and the old tag** first, then rerun the workflow. For anything that has been distributed publicly, prefer publishing a new version instead of rewriting history.

## Versioning

Use semantic versioning:

- `v0.x.y` while the flow is still being validated and may change significantly;
- increment the minor version for new behavior or policy changes;
- increment the patch version for compatible fixes/documentation corrections;
- reserve `v1.0.0` for a broadly validated stable flow.

The repository copy remains at `flows/Darkroast_Lightroast_Master_AV1_QSV.json`; users should normally download the versioned JSON asset from Releases.
