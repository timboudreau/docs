---
title: Building with Docker
nav: false
---

The Docker based build image exactly replicates the official build process and is a quick way to get up and running with the full cross compiled setup. Start by getting the build image. It is fairly large (about 1.8 GB).

```
$ docker pull syncthing/build:latest
```

Then check out and build Syncthing.

```
$ git clone https://github.com/syncthing/syncthing
$ cd syncthing
$ ./build.sh docker-all
```

A full build is done for all supported architectures, and tests are run. The process should end with a bunch of release files (`.tar.gz` and `.zip`) created.
