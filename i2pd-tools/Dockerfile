FROM alpine:latest AS builder

# install build dependencies
RUN apk add --no-cache \
    build-base \
    make \
    boost-dev \
    openssl-dev \
    zlib-dev

# copy sources into the layer
COPY ./i2pd-tools /opt/i2pd-tools
WORKDIR /opt/i2pd-tools

# build all the tools
RUN make -j"$(nproc)"
RUN mkdir ./bin && \
    find . -maxdepth 1 -type f -executable -exec install -Dm755 {} ./bin \; && \
    rm ./bin/Makefile ./bin/README.md && \
    strip /opt/i2pd-tools/bin/* || true

FROM alpine:latest

# install runtime dependencies
RUN apk add --no-cache \
    boost-program_options \
    boost-system \
    openssl \
    zlib

# copy binaries to base layer
COPY --from=builder /opt/i2pd-tools/bin /usr/local/bin
WORKDIR /data

# drop into a shell by default so you can run any tool interactively
CMD ["sh"]
