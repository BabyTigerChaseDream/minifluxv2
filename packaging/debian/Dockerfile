ARG BASE_IMAGE_ARCH="amd64"

FROM ${BASE_IMAGE_ARCH}/golang:buster AS build

ADD . /src

# blocking github workaround
ENV GO111MODULE=on
ENV GOPROXY=https://goproxy.cn,direct
ENV GOSUMDB="sum.golang.google.cn"

ENV DEBIAN_FRONTEND noninteractive

COPY --from=builder /src .
RUN CGO_ENABLED=0 go get -ldflags '-s -w -extldflags -static' github.com/go-delve/delve/cmd/dlv
RUN apt-get update -q && \
    apt-get install -y -qq build-essential devscripts dh-make dh-systemd && \
    mkdir -p /build/debian && \
    mkdir /pkg/

RUN CGO_ENABLED=0 GOOS=linux go build -gcflags="all=-N -l" -o main ./...
COPY --from=builder /go/bin/dlv /

CMD ["/src/packaging/debian/build.sh"]
