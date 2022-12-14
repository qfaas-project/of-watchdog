# QFaaS Note

Based on quic-go commit `40b124d`: solved many issues for the 0-RTT feature.

- SupportedVersions = []VersionNumber{VersionDraft29, VersionDraft34, VersionDraft32} 
- This version suppoert `go` `1.14`, `1.15`, and `1.16`


## Implement `roundTripper.CloseAfterHandshakeConfirmed()` 

Diff the following files:
- http3/client.go
- http3/roundtrip.go
- interface.go
- session.go

## Bugs of issue 776 

https://github.com/lucas-clemente/quic-go/issues/765

if a H3 client is timeout, we cannot resue it because
 - The lower level session is cancelled
 - The `streams_map_outgoing_bidi.go` still records the error msg of timeout
 - The H3 client will get the error msg from stream and kill itself 

This is not a problem when we manual close the H3 client. Because if will empty the clients array in the H3 roundtrip. 
Thus will generate a new session when reuse the H3 client when call RoundTrip. 



# Original quic-go Readme

# A QUIC implementation in pure Go

<img src="docs/quic.png" width=303 height=124>

[![PkgGoDev](https://pkg.go.dev/badge/github.com/lucas-clemente/quic-go)](https://pkg.go.dev/github.com/lucas-clemente/quic-go)
[![Travis Build Status](https://img.shields.io/travis/lucas-clemente/quic-go/master.svg?style=flat-square&label=Travis+build)](https://travis-ci.org/lucas-clemente/quic-go)
[![CircleCI Build Status](https://img.shields.io/circleci/project/github/lucas-clemente/quic-go.svg?style=flat-square&label=CircleCI+build)](https://circleci.com/gh/lucas-clemente/quic-go)
[![Windows Build Status](https://img.shields.io/appveyor/ci/lucas-clemente/quic-go/master.svg?style=flat-square&label=windows+build)](https://ci.appveyor.com/project/lucas-clemente/quic-go/branch/master)
[![Code Coverage](https://img.shields.io/codecov/c/github/lucas-clemente/quic-go/master.svg?style=flat-square)](https://codecov.io/gh/lucas-clemente/quic-go/)

quic-go is an implementation of the [QUIC](https://en.wikipedia.org/wiki/QUIC) protocol in Go. It implements the [IETF QUIC draft-29](https://tools.ietf.org/html/draft-ietf-quic-transport-29), [draft-32](https://tools.ietf.org/html/draft-ietf-quic-transport-32) and [draft-34](https://tools.ietf.org/html/draft-ietf-quic-transport-34).

## Version compatibility

Since quic-go is under active development, there's no guarantee that two builds of different commits are interoperable. The QUIC version used in the *master* branch is just a placeholder, and should not be considered stable.

When using quic-go as a library, please always use a [tagged release](https://github.com/lucas-clemente/quic-go/releases). Only these releases use the official draft version numbers.

## Guides

*We currently support Go 1.15+, with [Go modules](https://github.com/golang/go/wiki/Modules) support enabled.*

Running tests:

    go test ./...

### QUIC without HTTP/3

Take a look at [this echo example](example/echo/echo.go).

## Usage

### As a server

See the [example server](example/main.go). Starting a QUIC server is very similar to the standard lib http in go:

```go
http.Handle("/", http.FileServer(http.Dir(wwwDir)))
http3.ListenAndServeQUIC("localhost:4242", "/path/to/cert/chain.pem", "/path/to/privkey.pem", nil)
```

### As a client

See the [example client](example/client/main.go). Use a `http3.RoundTripper` as a `Transport` in a `http.Client`.

```go
http.Client{
  Transport: &http3.RoundTripper{},
}
```

## Contributing

We are always happy to welcome new contributors! We have a number of self-contained issues that are suitable for first-time contributors, they are tagged with [help wanted](https://github.com/lucas-clemente/quic-go/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22). If you have any questions, please feel free to reach out by opening an issue or leaving a comment.
