# A QUIC implementation in pure Go

**Please read https://multipath-quic.org/2017/12/09/artifacts-available.html to figure out how to setup the code.**

<img src="docs/quic.png" width=303 height=124>

[![Godoc Reference](https://img.shields.io/badge/godoc-reference-blue.svg?style=flat-square)](https://godoc.org/github.com/lucas-clemente/quic-go)
[![Linux Build Status](https://img.shields.io/travis/lucas-clemente/quic-go/master.svg?style=flat-square&label=linux+build)](https://travis-ci.org/lucas-clemente/quic-go)
[![Windows Build Status](https://img.shields.io/appveyor/ci/lucas-clemente/quic-go/master.svg?style=flat-square&label=windows+build)](https://ci.appveyor.com/project/lucas-clemente/quic-go/branch/master)
[![Code Coverage](https://img.shields.io/codecov/c/github/lucas-clemente/quic-go/master.svg?style=flat-square)](https://codecov.io/gh/lucas-clemente/quic-go/)

quic-go is an implementation of the [QUIC](https://en.wikipedia.org/wiki/QUIC) protocol in Go.

## Roadmap

quic-go is compatible with the current version(s) of Google Chrome and QUIC as deployed on Google's servers. We're actively tracking the development of the Chrome code to ensure compatibility as the protocol evolves. In that process, we're dropping support for old QUIC versions.
As Google's QUIC versions are expected to converge towards the [IETF QUIC draft](https://github.com/quicwg/base-drafts), quic-go will eventually implement that draft.

## Guides

We currently support Go 1.9+.

Installing and updating dependencies:

    go get -t -u ./...

Running tests:

    go test ./...

### Running the example server

    go run example/main.go -www /var/www/

Using the `quic_client` from chromium:

    quic_client --host=127.0.0.1 --port=6121 --v=1 https://quic.clemente.io

Using Chrome:

    /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --user-data-dir=/tmp/chrome --no-proxy-server --enable-quic --origin-to-force-quic-on=quic.clemente.io:443 --host-resolver-rules='MAP quic.clemente.io:443 127.0.0.1:6121' https://quic.clemente.io

### QUIC without HTTP/2

Take a look at [this echo example](example/echo/echo.go).

### Using the example client

    go run example/client/main.go https://clemente.io


## mp-quic

Install mp-quic using the following commands:

```bash
# Get upstream quic-go code
go get github.com/lucas-clemente/quic-go

# Add the mp-quic remote and checkout to the prototype branch
cd $GOPATH/src/github.com/lucas-clemente/quic-go
git remote add mp-quic git@github.com:umr-ds/mp-quic.git
git fetch mp-quic && git checkout conext17

# Download all dependency packages
go get -t -u ./...
```

If a build error like *mc.conn.State undefined* occurs, an older version of *github.com/bifurcation/mint* can be checked out:

```bash
cd $GOPATH/src/github.com/bifurcation/mint
git reset --hard a6080d464fb57a9330c2124ffb62f3c233e3400e

# Check that no more build issues arise
cd $GOPATH/src/github.com/lucas-clemente/quic-go
go build
# This should not produce any outputw
```

### mp-quic on android

```bash
# install and init gomobile
go get golang.org/x/mobile/cmd/gomobile
gomobile init

# compile
cd $GOPATH/src/github.com/lucas-clemente/quic-go
CC="$NDK_ROOT/bin/arm-linux-androideabi-gcc" GOOS=linux GOARCH=arm GOARM=7 CGO_ENABLED=0 go build -v -o quic-example-client example/client/main.go

# install compiled binary
adb push quic-example-client /sdcard
adb shell 'su -c "mount -o remount,rw /system"'
adb shell 'su -c "cp /sdcard/quic-example-client /system/bin/quic-example-client"'
adb shell 'su -c "chmod 700 /system/bin/quic-example-client"'
```

## Usage

### As a server

See the [example server](example/main.go) or try out [Caddy](https://github.com/mholt/caddy) (from version 0.9, [instructions here](https://github.com/mholt/caddy/wiki/QUIC)). Starting a QUIC server is very similar to the standard lib http in go:

```go
http.Handle("/", http.FileServer(http.Dir(wwwDir)))
h2quic.ListenAndServeQUIC("localhost:4242", "/path/to/cert/chain.pem", "/path/to/privkey.pem", nil)
```

### As a client

See the [example client](example/client/main.go). Use a `h2quic.RoundTripper` as a `Transport` in a `http.Client`.

```go
http.Client{
  Transport: &h2quic.RoundTripper{},
}
```

## Contributing

We are always happy to welcome new contributors! We have a number of self-contained issues that are suitable for first-time contributors, they are tagged with [want-help](https://github.com/lucas-clemente/quic-go/issues?q=is%3Aopen+is%3Aissue+label%3Awant-help). If you have any questions, please feel free to reach out by opening an issue or leaving a comment.
