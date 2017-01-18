# shadowsocks-libev on Debian/Ubuntu
# Version: 0.0.1

FROM alpine
MAINTAINER sudoz <me@isudox.com>

ENV VER 2.5.6
ENV URL https://github.com/shadowsocks/shadowsocks-libev/archive/v$VER.tar.gz
ENV DIR shadowsocks-libev-$VER
ENV DEP asciidoc autoconf build-base curl libtool linux-headers openssl-dev pcre-dev tar xmlto

ENV ADDR      0.0.0.0
ENV PORT      8388
ENV METHOD    aes-256-cfb
ENV TIMEOUT   300
ENV DNS       8.8.8.8
ENV PASSWORD=

RUN set -ex && \
    apk add --no-cache --virtual .build-deps $DEP && \
    curl -sSL $URL | tar xz && \
    cd $DIR && \
        ./configure --disable-documentation && \
        make install && \
        cd .. && \
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
              -u

# docker run -d -e METHOD=aes-256-cfb -e PASSWORD=123456 -p 8388:8388 --restart always sudoz/shadowsocks-libev