FROM alpine:3.19 AS compiler

ARG CONFIGURE_FLAGS='CXXFLAGS=-Werror -Wno-error=builtin-declaration-mismatch'
ARG MAKE_FLAGS="--jobs=6"
ARG RATHENA_SOURCE_REPO=https://github.com/rathena/rathena.git

LABEL org.opencontainers.image.source="https://github.com/mestre8d/rathena-docker"
LABEL org.opencontainers.image.description="Contains a rAthena compilation environment in Alpine Linux"

RUN apk update && apk add --no-cache bash=5.2.21-r0 ca-certificates=20240226-r0 cmake=3.27.8-r0 \
    g++=13.2.1_git20231014-r0 gcc=13.2.1_git20231014-r0 gdb=14.1-r0 git=2.43.5-r0 \
    linux-headers=6.5-r0 make=4.4.1-r2 mariadb-dev=10.11.6-r0 netcat-openbsd=1.226-r0 valgrind=3.22.0-r0 \
    wget=1.21.4-r0 zlib-dev=1.3.1-r0 \
    && mkdir -p /app

WORKDIR /app

RUN git clone $RATHENA_SOURCE_REPO . && rm -rf .git

RUN ./configure "$CONFIGURE_FLAGS" && make server "$MAKE_FLAGS"

RUN chmod a+x login-server char-server map-server web-server

FROM alpine:3.19 AS release

LABEL org.opencontainers.image.source="https://github.com/mestre8d/rathena-docker"
LABEL org.opencontainers.image.description="Contains a rAthena release environment in Alpine Linux."

RUN adduser -h /opt/rathena -s /bin/sh -D -u 1000 rathena

RUN apk update && apk add --no-cache mariadb-dev=10.11.6-r0 zlib-dev=1.3.1-r0

WORKDIR /opt/rathena

COPY --from=compiler /app/map-server /opt/rathena/map-server
COPY --from=compiler /app/char-server /opt/rathena/char-server
COPY --from=compiler /app/login-server /opt/rathena/login-server

COPY --from=compiler /app/db /opt/rathena/db
COPY --from=compiler /app/conf /opt/rathena/conf
COPY --from=compiler /app/npc /opt/rathena/npc

RUN chown 1000:1000 -R /opt/rathena

USER 1000

EXPOSE 6900 6121 5121