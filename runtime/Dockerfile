ARG PHP_BASE

FROM $PHP_BASE as builder

RUN apk add --no-cache \
    linux-headers \
    hiredis-dev \
    openssl-dev \
    libc-dev \
    pcre-dev \
    autoconf \
    re2c \
    make \
    gcc \
    g++ \
    gc

RUN docker-php-ext-install bcmath

ADD ext-installer /usr/bin

ARG AST_VER
RUN ext-installer ast $AST_VER

ARG PROTO_VER
RUN ext-installer protobuf $PROTO_VER

ARG SWOOLE_VER
RUN ext-installer swoole $SWOOLE_VER '--enable-async-redis --enable-mysqlnd --enable-openssl'

FROM $PHP_BASE

ARG TZ=PRC
RUN apk add --no-cache tzdata && \
    cp /usr/share/zoneinfo/${TZ} /etc/localtime && \
    echo "${TZ}" > /etc/timezone && \
    apk del tzdata

RUN apk add --no-cache \
    inotify-tools \
    libstdc++ \
    hiredis \
    openssl

COPY --from=builder /usr/local/lib/php/extensions /usr/local/lib/php/extensions

RUN docker-php-ext-enable ast protobuf swoole bcmath

WORKDIR "/app"
