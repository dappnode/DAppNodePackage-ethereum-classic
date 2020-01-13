# Build Parity in a stock alpine builder container
FROM alpine:edge as builder

ARG PARITY_VERSION=v2.5.13

# show backtraces
ENV RUST_BACKTRACE 1

RUN apk --no-cache add \
    build-base \
    cargo \
    cmake \
    eudev-dev \
    linux-headers \
    perl \
    rust \ 
    git

RUN git clone -b ${PARITY_VERSION} https://github.com/paritytech/parity-ethereum /parity-ethereum
WORKDIR /parity-ethereum
RUN cargo build 
RUN cargo build --release --features final --target x86_64-alpine-linux-musl
RUN strip target/x86_64-alpine-linux-musl/release/parity

# Pull Parity into a second stage deploy alpine container
FROM alpine:edge

RUN apk --no-cache add \
    libstdc++ \ 
    eudev-libs \
    libgcc \
    bash

RUN apk add -u musl

COPY --from=builder /parity-ethereum/target/x86_64-alpine-linux-musl/release/parity /usr/local/bin

ENTRYPOINT [ "sh", "-c", "exec parity --chain classic --jsonrpc-port 8545 --jsonrpc-interface all --jsonrpc-hosts all --jsonrpc-cors all --ws-interface 0.0.0.0 --ws-port 8546 --ws-origins all --ws-hosts all $EXTRA_OPTS" ]

