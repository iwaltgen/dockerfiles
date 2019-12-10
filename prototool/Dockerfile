FROM golang:1.12.4-alpine3.9 as builder

RUN apk add --update --no-cache git upx && \
  rm -rf /var/cache/apk/*

ENV PGV_VERSION=0.1.0
RUN GO111MODULE=on go get \
  github.com/envoyproxy/protoc-gen-validate@v${PGV_VERSION} && \
  mv /go/bin/protoc-gen-* /usr/local/bin/

RUN upx --lzma /usr/local/bin/*

FROM uber/prototool

COPY --from=builder /usr/local/bin /usr/local/bin

ENV PGV_VERSION=0.1.0
