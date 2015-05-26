---
layout: page
title: Jenkins CI
nav: true
weight: 10
---

Jenkins will test pull requests from recognized authors. The tests performed are currently these:

 - "Merged build": This does a regular build of all platforms plus runs unit tests for the merged pull request. Must be successful for the PR to be mergeable.

 - "gofmt": A simple style check that all Go source files are compliant with gofmt formatting. Must be successful for the PR to be mergeable.

 - "authors": Checks whether the PR author is listed in the AUTHORS file. Will fail for all first time contributors, which is fine. This is merely a flag for the merging developer to make sure AUTHORS, NICKS and the contributor list in the GUI is are updated when merging the PR.

If the pull request is not from a recognized author, an *admin* needs to tell Jenkins to perform the tests, after giving the patch a manual look over to prevent shenanigans.

To enable testing for this pull request only:

```
@st-jenkins ok to test
```

To enable testing for this pull request, and all future pull requests from the same author:

```
@st-jenkins add to whitelist
```

For pull requests where Jenkins has already run it's tests, but should run them again:

```
@st-jenkins test this please
```

This is not necessary when new commits are pushed (tests will be rerun for the new commits), but is useful if something has changed server side or to verify that everything is still OK if `master` has been updated.
