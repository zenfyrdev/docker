FROM python:3.11-alpine AS builder

COPY ./src /build
WORKDIR /build

RUN apk add --no-cache git cargo libgcc
RUN pip3 install --no-cache-dir setuptools wheel
RUN pip3 install .
RUN apk del git cargo && rm -rf /build

FROM python:3.11-alpine

RUN adduser -D -u 1000 app
USER app

COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=builder /usr/local/bin/xmpp-discord-bridge /usr/local/bin/

ENTRYPOINT ["xmpp-discord-bridge"]
