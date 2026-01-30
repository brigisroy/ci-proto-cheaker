# ci-proto-checker

**ci-proto-checker** is a lightweight CI/CD tool for validating, linting, and compiling Protocol Buffer (protobuf) definitions in Go/gRPC projects. It uses a minimal Docker image to ensure consistent protobuf code generation and compatibility checks across pipelines.

## Features

- Protobuf compilation to Go with gRPC support.
- Dependency installation for `protoc-gen-go` and `protoc-gen-go-grpc`.
- Alpine-based for fast, secure CI jobs.
- Supports linting and breaking change detection (extendable with Buf or similar).[^1]


## Dockerfile Overview

```
FROM golang:1.25-alpine3.22

# Install system packages
RUN apk update && apk add --no-cache git protobuf make build-base

# Install Go tools globally
RUN go install google.golang.org/protobuf/cmd/protoc-gen-go@latest && \
    go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest

# Set PATH for installed Go binaries
ENV PATH="$PATH:$(go env GOPATH)/bin"
```

This image provides a ready-to-use `protoc` environment.

## Quick Start

Build the image:

```
docker build -t ci-proto-checker .
```

Run proto compilation (mount your protos):

```
docker run --rm -v $(pwd)/protos:/protos ci-proto-checker \
  protoc --go_out=/protos --go-grpc_out=/protos /protos/*.proto
```


## CI Usage Example (GitLab CI)

```yaml
proto-check:
  image: ci-proto-checker
  script:
    - protoc --version
    - protoc -I protos --go_out=protos --go-grpc_out=protos protos/*.proto
```

Add Buf for linting/compatibility:

```
apk add buf  # Or use bufbuild/buf image
buf lint
buf breaking --against ".git#branch=main"
```


## Customization

- Extend with `buf` for advanced checks: Add `RUN apk add buf` or multi-stage.
- For validation: Install `protoc-gen-validate`.[^2]


## Contributing

Fork, add features (e.g., Temporal integration for workflows), and submit PRs. Tests via `make test`.
<span style="display:none">[^10][^11][^12][^13][^3][^4][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: programming.docker_go_alpine

[^2]: projects.ci_proto_checker

[^3]: https://github.com/well-known-components/proto-compatibility-tool

[^4]: tools.go_protobuf_grpc

[^5]: https://github.com/bufbuild/protoc-gen-validate

[^6]: https://github.com/namely/docker-protoc/blob/master/README.md

[^7]: https://buf.build/bufbuild/protovalidate/docs/d74e1912737946d8b46a871e2d29f030

[^8]: https://pkg.go.dev/github.com/envoyproxy/protoc-gen-validate

[^9]: https://gitlab.com/protobuf-tools/proto-publish

[^10]: https://mionskowski.pl/posts/ci-pipeline-for-protobuf/

[^11]: https://docs.docker.com/scout/policy/ci/

[^12]: https://jsontotable.org/protobuf-viewer

[^13]: https://github.com/protobuf-c/protobuf-c

