FROM golang:1.24-alpine as builder
ENV CGO_ENABLED=1

WORKDIR /app
RUN apk add gcc musl-dev patch
COPY ./core .
RUN go build -o /usr/bin/spindle -ldflags '-s -w -extldflags "-static"' ./cmd/spindle

FROM alpine:edge
EXPOSE 6555

LABEL org.opencontainers.image.title='spindle'
LABEL org.opencontainers.image.description='small CI runner service for tangled'
LABEL org.opencontainers.image.source='https://tangled.sh/@tangled.sh/core'
LABEL org.opencontainers.image.url='https://tangled.sh'
LABEL org.opencontainers.image.vendor='tangled.sh'
LABEL org.opencontainers.image.licenses='MIT'

RUN adduser -D -u 1000 git

RUN mkdir -p /home/git/repositories && chown -R git:git /home/git
COPY --from=builder /usr/bin/spindle /usr/bin
RUN mkdir /app && chown -R git:git /app

USER git

HEALTHCHECK --interval=60s --timeout=30s --start-period=5s --retries=3 \
    cmd curl -f http://localhost:6555 || exit 1

ENTRYPOINT ["/usr/bin/spindle"]
