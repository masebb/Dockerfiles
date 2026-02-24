FROM golang:1.25-bookworm AS builder

ENV CGO_ENABLED=0

ARG BUILD_VERSION=main

RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates git && \
    rm -rf /var/lib/apt/lists/*

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    git clone --depth 1 --branch "$BUILD_VERSION" https://github.com/SavaLione/go-mirror-zig.git /src/go-mirror-zig && \
    cd /src/go-mirror-zig && \
    go mod download && \
    go build -v -o /out/go-mirror-zig ./cmd/main.go


FROM debian:bookworm-slim AS runtime

RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates && \
    rm -rf /var/lib/apt/lists/*

COPY --from=builder /out/go-mirror-zig /usr/local/bin/go-mirror-zig

RUN mkdir -p /data /acme

WORKDIR /data

VOLUME ["/data", "/acme"]

EXPOSE 80/tcp
EXPOSE 443/tcp

ENTRYPOINT ["go-mirror-zig"]
CMD ["-cache-dir=/data"]
