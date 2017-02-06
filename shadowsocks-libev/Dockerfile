# shadowsocks-libev on Debian/Ubuntu
# Version: 0.0.2

FROM alpine
MAINTAINER sudoz <me@isudox.com>

ENV VER 3.0.0
ENV URL https://github.com/shadowsocks/shadowsocks-libev/releases/download/v$VER/shadowsocks-libev-$VER.tar.gz
ENV DIR shadowsocks-libev-$VER
ENV DEP autoconf build-base curl libev-dev libtool linux-headers libsodium-dev mbedtls-dev pcre-dev tar udns-dev
ENV ADDR      0.0.0.0
ENV PORT      8388
ENV METHOD    aes-256-cfb
ENV TIMEOUT   300
ENV DNS       8.8.8.8
ENV DNS_2     8.8.4.4
ENV PASSWORD=

RUN set -ex && \
    apk add --no-cache --virtual .build-deps $DEP && \
    curl -sSL $URL | tar xz && \
    cd $DIR && \
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
    rm -rf $DIR

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
              -u

# docker run -d -e METHOD=aes-256-cfb -e PASSWORD=123456 -p 8388:8388 --restart always sudoz/shadowsocks-libev