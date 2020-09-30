# Release Actions
A proof-of-concept workflow automating a possible process to cut a release from a repository that uses either https://trunkbaseddevelopment.com/branch-for-release/ or https://trunkbaseddevelopment.com/release-from-trunk/ models. The implemented workflow supports both transparently.

![master build](https://github.com/jb185293/release-actions/workflows/.github/workflows/actions.yml/badge.svg?branch=master&event=push)

## Repo setup
The repo so structured so that dev changes happen in master (either directly or via PRs) and the GH workflow automatically builds them, using the VERSION file to determine target product version.
Similar build can be defined for PR validation and other branches, but that is out of scope of this PoC.

## Cutting a release
The desired "release" state is:
1. A commit of the release version is tagged
2. Build artifacts from the tagged commit are created and stored somewhere
3. GitHub Release is created from the tag, contains the artifacts for convenience, and other useful information
4. Master branch's VERSION file is incremented to reflect that master should build a newer version (only happens on major or minor releases, not on patch (hotfixes))

## The Workflow
This implementation serves as a PoC that the above process can be automated. The details may (should) change if you want to use this in your repo.

The workflow consists of three jobs, all are initiated on push to `master` or `release/*` branch, or on pushing a new version tag (`v*`, expecting `vX.Y.Z`). This comes from a repo where release for version 20.10.0 would be marked by a release branch `release/20.10.0` and in this flow also tagged as `v20.10.0`.

### Common build
Whether you're push a new commit to master, a release branch, or mark the final release version with the right tag, what happens first is checkout and build of the appropriate git ref. This validates the commit and prepares the artifacts.

### Creating GH Release
If the workflow was initiated by a release tag and not just any other commit push, it creates a GH release draft from the tag and adds links as assets to the release.

### Master Version Bump
A release can be either a regular release, or a hotfix or other necessary out-of-band cut. When a new regular end-of-sprint release is created, we also need to bump the version in VERSION file to mark the dev builds accordingly. This is also handled by the workflow based on a simple match - if the tag has the right format but ends with `.0`, it's treated as a "bumping" release. For all other releases this job is skipped.

## Manual Release Steps
Even with this workflow there is manual work to be done. First and foremost the release has to be initiated somehow. This is done by a developer pushing the version tag to GH. This is enough information for the workflow to start.
For now this workflow creates the GH Release as a draft so that the developers can review it and if needed make some changes, even dropping it completely and creating the release from another commit. Once the release is deemed ready, it has to be manually published.

testing 1.19 release
