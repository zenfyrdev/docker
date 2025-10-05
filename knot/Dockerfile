FROM golang:1.24-alpine as builder
ENV KNOT_REPO_SCAN_PATH=/home/git/repositories
ENV CGO_ENABLED=1

WORKDIR /app
RUN apk add gcc musl-dev
COPY ./core .
RUN go build -o /usr/bin/knot -ldflags '-s -w -extldflags "-static"' ./cmd/knot

FROM alpine:edge
EXPOSE 5555
EXPOSE 22

LABEL org.opencontainers.image.title='knot'
LABEL org.opencontainers.image.description='data server for tangled'
LABEL org.opencontainers.image.source='https://tangled.sh/@tangled.sh/knot-docker'
LABEL org.opencontainers.image.url='https://tangled.sh'
LABEL org.opencontainers.image.vendor='tangled.sh'
LABEL org.opencontainers.image.licenses='MIT'

COPY rootfs .
RUN chmod 755 /etc
RUN chmod -R 755 /etc/s6-overlay
RUN apk add shadow s6-overlay execline openssl openssh git curl bash
RUN useradd -d /home/git git && openssl rand -hex 16 | passwd --stdin git
RUN mkdir -p /home/git/repositories && chown -R git:git /home/git
COPY --from=builder /usr/bin/knot /usr/bin
RUN mkdir /app && chown -R git:git /app

HEALTHCHECK --interval=60s --timeout=30s --start-period=5s --retries=3 \
    cmd curl -f http://localhost:5555 || exit 1

ENTRYPOINT ["/init"]
