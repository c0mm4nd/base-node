# BUILD THE op-node
FROM us-docker.pkg.dev/oplabs-tools-artifacts/images/op-node:v1.13.2 AS op
# FROM golang:1.22 AS op

# WORKDIR /app

# ENV REPO=https://github.com/ethereum-optimism/optimism.git
# ENV VERSION=v1.13.2
# ENV COMMIT=c8b9f62736a7dad7e569719a84c406605f4472e6
# RUN git clone $REPO --branch op-node/$VERSION --single-branch . && \
#     git switch -c branch-$VERSION && \
#     bash -c '[ "$(git rev-parse HEAD)" = "$COMMIT" ]'

# # Install 'just'
# RUN curl -sSfL 'https://just.systems/install.sh' | bash -s -- --to /usr/local/bin

# RUN cd op-node && \
#     make VERSION=$VERSION op-node

# # BUILD THE reth
FROM ghcr.io/paradigmxyz/op-reth:v1.3.12 AS reth
# FROM rust:1.82 AS reth

# ARG FEATURES=jemalloc,asm-keccak,optimism

# WORKDIR /app

# RUN apt-get update && apt-get -y upgrade && apt-get install -y git libclang-dev pkg-config curl build-essential

# ENV REPO=https://github.com/paradigmxyz/reth.git
# ENV VERSION=v1.2.0
# ENV COMMIT=1e965caf5fa176f244a31c0d2662ba1b590938db
# RUN git clone $REPO --branch $VERSION --single-branch . && \
#     git switch -c branch-$VERSION && \
#     bash -c '[ "$(git rev-parse HEAD)" = "$COMMIT" ]'

# RUN cargo build --bin op-reth --features $FEATURES --profile maxperf --manifest-path crates/optimism/bin/Cargo.toml

# THE REAL RUNTIME
FROM m.daocloud.io/docker.io/library/ubuntu:22.04

RUN apt-get update && \
    apt-get install -y jq curl supervisor && \
    rm -rf /var/lib/apt/lists
RUN mkdir -p /var/log/supervisor

WORKDIR /app

COPY --from=op /usr/local/bin/op-node ./
COPY --from=reth /usr/local/bin/op-reth ./
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY ./reth/reth-entrypoint ./execution-entrypoint
COPY op-node-entrypoint .

CMD ["/usr/bin/supervisord"]
