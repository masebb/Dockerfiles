FROM golang:1.22-bookworm AS builder

ENV CGO_ENABLED=0

ARG BUILD_VERSION
RUN if [ -z "$BUILD_VERSION" ]; then echo "BUILD_VERSION is required" >&2; exit 1; fi


RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates git

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    git clone --depth 1 --branch "$BUILD_VERSION" https://github.com/osrg/gobgp.git /src/gobgp; \
    cd /src/gobgp; \
    go mod download; \
    mkdir -p /out; \
    go build -v -trimpath -ldflags="-s -w" -o /out/gobgp ./cmd/gobgp; \
    go build -v -trimpath -ldflags="-s -w" -o /out/gobgpd ./cmd/gobgpd


FROM debian:bookworm-slim AS runtime

LABEL org.opencontainers.image.title="GoBGP" \
    org.opencontainers.image.description="GoBGP daemon and CLI on debian-slim" \
    org.opencontainers.image.source="https://github.com/osrg/gobgp"

RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates iproute2 libcap2-bin

COPY --from=builder /out/gobgpd /usr/local/bin/gobgpd
COPY --from=builder /out/gobgp  /usr/local/bin/gobgp

RUN mkdir -p /

EXPOSE 179/tcp

WORKDIR /

ENTRYPOINT ["gobgpd"]
CMD ["-f", "/config/gobgp.toml"]

