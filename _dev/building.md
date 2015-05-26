---
layout: page
title: Building Syncthing
nav: true
weight: 0
---

<p class="message warning">
You probably only need to go through the build process if you are going to do
development on syncthing or if you need to do a special packaging of it. For all other purposes we recommend using the the <a href="https://github.com/syncthing/syncthing/releases/latest">official binary releases</a> instead.
</p>

<p class="message warning">
If you're on Linux and want the quickest possible start, check out <a href="/dev/building-with-docker.html">Building with Docker</a>. Otherwise follow the guide below to set up the development environment on your computer.
</p>

## Branches and Tags

You should base your work on the `master` branch when doing your
development. This branch is usually what will be going into the next
release and always what pull requests should be based on.

If you're looking to build and package a release of syncthing you should
instead use the latest tag (`vX.Y.Z`)  as the contents of `master` may
be unstable and unsuitable for general consumption.

## Prerequisites

* Go **1.3** or higher
* Git (`git`) and Mercurial (`hg`)

If you're not a Go developer since before, the easiest way to get going is to download the latest version of Go as instructed in http://golang.org/doc/install and `export GOPATH=~`.

## Building (Unix)

* Install the prerequisites.
* Open a terminal.

```bash
# This should output "go version go1.3" or higher.
$ go version

# Go is particular about file locations; use this path unless you know very
# well what you're doing.
$ mkdir -p ~/src/github.com/syncthing
$ cd ~/src/github.com/syncthing
# Note that if you are building from a source code archive, you need to 
# rename the directory from syncthing-XX.YY.ZZ to syncthing
$ git clone https://github.com/syncthing/syncthing

# Now we have the source. Time to build!
$ cd syncthing

# You should be inside ~/src/github.com/syncthing/syncthing right now.
$ go run build.go
```

Unless something goes wrong, you will have a `syncthing` binary built and ready in `~/src/github.com/syncthing/syncthing/bin`.

## Building (Windows)

* Install the prerequisites.
* Open a `cmd` Window.

```bash
# This should output "go version go1.3" or higher.
> go version

# Go is particular about file locations; use this path unless you know very
# well what you're doing.
> mkdir c:\src\github.com\syncthing
> cd c:\src\github.com\syncthing
# Note that if you are building from a source code archive, you need to 
# rename the directory from syncthing-XX.YY.ZZ to syncthing
> git clone https://github.com/syncthing/syncthing

# Now we have the source. Time to build!
> cd syncthing
> go run build.go
```

Unless something goes wrong, you will have a `syncthing.exe` binary built and ready in `c:\src\github.com\syncthing\syncthing\bin`.

## Subcommands and Options

The following `build.go` subcommands and options exist.

 * `go run build.go install` -- installs binaries in `./bin` (default command, this is what happens when build.go is run without any commands or parameters).

 * `go run build.go build` -- forces a rebuild of the binary to the current directory; similar to `install` but slower.

 * `go run build.go clean` -- remove build artefacts, guaranteeing a complete rebuild. Use this when switching between normal builds and noupgrade builds.

 * `go run build.go test` -- run the tests.

 * `go run build.go tar` -- create a syncthing tar.gz dist file in the current directory. Assumes a Unixy build.

 * `go run build.go zip` -- create a syncthing zip dist file in the current directory. Assumes a Windows build.

 * `go run build.go  assets` -- rebuild the compiled-in GUI assets.

 * `go run build.go  deps` -- update the in-repo dependencies.

 * `go run build.go  xdr` -- regenerate the XDR en/decoders. Only necessary when the protocol has changed.

The options `-no-upgrade`, `-goos` and `-goarch` can be given to influence `install`, `build`, `tar` and `zip`. Examples:

 * `go run build.go -goos linux -goarch 386 tar` -- build a tar.gz distribution of syncthing for linux-386.

 * `go run build.go -goos windows -no-upgrade zip` -- build a zip distribution of syncthing for Windows (current architecture) with upgrading disabled.

## Building without Git

Syncthing can be built perfectly fine from a source tarball of course. The process is almost the same as above, with the difference being that it doesn't know what version it is because that is determined from Git tags. To overcome this you must pass the `-version` flag to build.go. If you are building something that will be installed as a package (Debian, RPM, ...) you almost certainly want to use `-no-upgrade` as well to prevent the built in upgrade system from being activated.

 * `go run build.go -version v0.10.26 -no-upgrade tar` -- build a tar.gz distribution of syncthing for the current OS/arch, tagged as `v0.10.26`, with upgrades disabled.
