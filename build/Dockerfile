# Build Core-Geth in a stock Go builder container
FROM golang:1.18-alpine as builder

ARG UPSTREAM_VERSION

RUN apk add --no-cache make gcc musl-dev linux-headers git bash

RUN git clone -b ${UPSTREAM_VERSION} https://github.com/etclabscore/core-geth.git && \
    cd core-geth && make geth

# Pull Geth into a second stage deploy alpine container
FROM alpine:latest

RUN apk add --no-cache ca-certificates

COPY --from=builder /go/core-geth/build/bin/geth /usr/local/bin

ENTRYPOINT geth --classic --http --http.addr 0.0.0.0 --http.corsdomain "*" --http.vhosts "*" --ws --ws.origins "*" --ws.addr 0.0.0.0 --port $P2P_PORT --syncmode ${SYNCMODE:-snap} $EXTRA_OPTS
