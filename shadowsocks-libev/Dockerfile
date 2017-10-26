# shadowsocks-libev on Debian/Ubuntu
# Version: 0.0.3

FROM alpine
MAINTAINER sudoz <me@isudox.com>

ARG VER=3.1.0
ARG URL=https://github.com/shadowsocks/shadowsocks-libev/releases/download/v$VER/shadowsocks-libev-$VER.tar.gz

ENV ADDR      0.0.0.0
ENV PORT      12134
ENV METHOD    aes-256-cfb
ENV TIMEOUT   300
ENV DNS       8.8.8.8
ENV DNS_2     8.8.4.4
ENV PASSWORD=
ENV ARGS=

RUN set -ex && \
    apk add --no-cache --virtual .build-deps \
                                autoconf \
                                build-base \
                                curl \
                                libev-dev \
                                libtool \
                                linux-headers \
                                libsodium-dev \
                                mbedtls-dev \
                                pcre-dev \
                                tar \
                                c-ares-dev && \
    cd /tmp && \
    curl -sSL $SS_URL | tar xz --strip 1 && \
    ./configure --prefix=/usr --disable-documentation && \
    make install && \
    cd .. && \

    runDeps="$( \
        scanelf --needed --nobanner /usr/bin/ss-* \
            | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
            | xargs -r apk info --installed \
            | sort -u \
    )" && \
    apk add --no-cache --virtual .run-deps $runDeps && \
    apk del .build-deps && \
    rm -rf /tmp/*

USER nobody

EXPOSE $PORT/tcp
EXPOSE $PORT/udp

CMD ss-server -s $ADDR \
              -p $PORT \
              -k ${PASSWORD:-$(hostname)} \
              -m $METHOD \
              -t $TIMEOUT \
              --fast-open \
              -d $DNS \
              -d $DNS_2 \
              -u \
              $ARGS

# docker run -d -e METHOD=aes-256-cfb -e PASSWORD=123456 -p 8388:8388 --restart always sudoz/shadowsocks-libev
