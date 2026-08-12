# Releasing

Releases are created by GitHub Actions so the downloadable Tdarr flow is always attached as a versioned JSON asset.

## Create a release

1. Open **Actions** in this repository.
2. Select **Create Release**.
3. Click **Run workflow**.
4. Enter the version, for example `v0.1.0`.
5. Enter a short release title.
6. Choose whether the release is a pre-release.
7. Run the workflow.

The workflow will:

- validate the semantic version;
- create the Git tag if it does not already exist;
- build the release from the exact tagged repository tree;
- validate the Tdarr flow JSON;
- copy the flow to a versioned asset such as `tdarr-av1-flow-v0.1.0.json`;
- calculate and publish its SHA-256 checksum;
- extract the matching release notes from `CHANGELOG.md`;
- create the GitHub Release and attach the JSON asset.

## Recreating a deleted release

Deleting a GitHub Release does not necessarily delete its Git tag. That is fine.

If the tag still exists, re-running **Create Release** with the same version will use the existing tag and rebuild the asset from that exact tagged commit.

If the tag does not exist, the workflow creates it from the commit on which the workflow is run.

## Before a stable release

Keep early releases marked as **pre-release** until the validation matrix in `docs/TESTING.md` has been exercised against representative media.
