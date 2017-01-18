# shadowsocks-libev on Debian/Ubuntu
# Version: 0.0.1

FROM alpine
MAINTAINER sudoz <me@isudox.com>

ENV VER 2.5.6
ENV URL https://github.com/shadowsocks/shadowsocks-libev/archive/v$VER.tar.gz
ENV DIR shadowsocks-libev-$SS_VER
ENV DEP asciidoc autoconf build-base curl libtool linux-headers openssl-dev pcre-dev tar xmlto

ENV ADDR    0.0.0.0
ENV PORT    8388
ENV ENCRYPT aes-256-cfb
ENV TIMEOUT 300
ENV DNS     8.8.8.8
ENV PWD=

RUN set -ex && \
    apk add --no-cache --virtual .build-deps $DEP && \
    cd /tmp && \
    curl -sSL $URL | tar xz --strip 1 && \
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

VOLUME ["/etc/"]

EXPOSE $PORT/tcp $PORT/udp

CMD ss-server -s $ADDR \
              -p $PORT \
              -k ${PASSWORD:-$(hostname)} \
              -m $ENCRYPT \
              -t $TIMEOUT \
              --fast-open \
              -d $DNS \
              -u

# docker run -d -e ENCRYPT=aes-256-cfb -e PWD=123456 -p 8388:8388 --restart always sudoz/shadowsocks-libev