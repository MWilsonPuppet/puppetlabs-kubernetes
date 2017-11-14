FROM golang:1.9.2-alpine3.6

ENV GOPATH /go
ENV USER root

RUN set -x && \
  apk --no-cache add git gcc libc-dev && \
  go get github.com/cloudflare/cfssl/cmd/... && \ 
  cd /go/src/github.com/cloudflare/cfssl && \
  go get github.com/GeertJohan/go.rice/rice && rice embed-go -i=./cli/serve && \
  mkdir bin && cd bin && \
  go build ../cmd/cfssl && \
  go build ../cmd/cfssljson && \
  go build ../cmd/mkbundle && \
  go build ../cmd/multirootca && \
  echo "Build complete."

FROM ruby:2.3.5-alpine
COPY --from=0 /go/src/github.com/cloudflare/cfssl/vendor/github.com/cloudflare/cfssl_trust /etc/cfssl
COPY --from=0 /go/src/github.com/cloudflare/cfssl/bin/ /usr/bin
COPY . /etc/k8s

RUN set -x && \
  apk --no-cache add git && \
  gem install thor

ENTRYPOINT ["/etc/k8s/kube_tool.rb", "build_hiera", "$1"]

