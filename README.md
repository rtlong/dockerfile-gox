# docker image rtlong/gox

This is a simple docker image meant to make it easy to build cross-compiled
binaries for Go projects despite not having a Go development environment
installed. Based on [google/golang][google-golang]

## Usage

This provides a ready-to-go [gox][] build env based on Go 1.3. To use, you must
either have `go get`-able code on some remote repository or have a local copy of
the code you wish to build (which is mountable into a Docker container).

Either way, the first runtime argument is the `go get`-style, namespaced name of
your project.

```bash
# the rest of the examples assume this is set, but this is just for illustration:
$ PROJECT="github.com/rtlong/docker-util"
```

### If you do have a local copy,
you need to mount it inside the container in the
appropriate directory within the `$GOPATH` at `/gopath`. For example, if you
want to build `github.com/rtlong/docker-util` and have a local clone of that
repo:

```bash
$ docker run -v /local/path:/gopath/src/$PROJECT rtlong/gox $PROJECT
```

As is the default with `gox`, the compiled binaries will be saved in the
project directory, which in this case was mounted in the container from your
local system, so you should now see binaries at `/local/path/docker-util_*`.

### Without a local clone, you can just run this:

```bash
$ docker run rtlong/gox $PROJECT
```

Now, the binaries are in the same place in the container filesystem, so to pull
one of them out, use `docker cp`:

```bash
$ docker cp $(docker run -l -q):/gopath/src/${PROJECT}/docker-util_darwin_amd64 ./
```

### Specifying `gox` options
Any additional arguments after the first will be sent directly to `gox`, so
you can control which targets to compile like so:

```bash
$ docker run rtlong/gox $PROJECT -osarch='darwin/amd64 linux/amd64'
```

[google-golang]: https://registry.hub.docker.com/u/google/golang/
[gox]: https://github.com/mitchellh/gox
