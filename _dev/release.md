---
layout: page
title: Creating a Release
nav: true
weight: 90
---

## Prerequisites

 - Push access to the syncthing repo, for pushing a new tag.

 - SSH account on build server, member of the `jenkins` group, for accessing and signing the releases.

 - The release signing key on your GPG keyring on your own computer (for signing the tag) and your account on the build server (for signing the release). In a pinch, having it just on the build server will do since you can run git there to create, sign and push the tag.

 - Your Github token in the `GITHUB_TOKEN` environment variable on the build server, for uploading the release.

## Process

Make sure the build seems sane. I.e. the build is clean on the build server, the integration tests pass without complaints. (Currently, the tests are a bit flaky, specifically the `TestSyncCluster...` ones. I'm not sure if the tests are weird or there is something actually bad happening that should be fixed - requires investigation).

Create a new, signed tag on master, with the version as comment, and push it:

```bash
$ git tag -a -s -u release@syncthing.net -m v0.10.15 v0.10.15
$ git push --tags
```

The build server will build packages under the job `syncthing-release`. Wait for this to complete successfully before moving on.

Run `./changelog.sh` (in the repo) to create the changelog comparison from the previous release. Copy to clipboard.

On the Github releases page, select the newly pushed tag and hit "Edit Tag". Set the "Release title" to the same version as the tag, paste in the changelog from above, and publish the release.

> :warning: At this point, syncthing instances that do an upgrade check will see the new version, but fail to upgrade since there are no files there yet, generating a warning. But files can't be uploaded by the release upload script until the release is published. So minimizing the time spent in this stage is a good idea.

On the build server, logged in via ssh, run `/usr/local/bin/upload-release`. This will create the md5sum and sha1sum files, sign them (gpg will prompt for key passphrase twice) and upload the whole shebang to Github.

Verify it looks sane on the releases page.
