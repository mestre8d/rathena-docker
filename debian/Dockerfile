FROM debian:bookworm-20251117 AS compiler

ARG CONFIGURE_FLAGS='CXXFLAGS=-Werror -Wno-error=builtin-declaration-mismatch'
ARG RATHENA_SOURCE_REPO=https://github.com/rathena/rathena.git

LABEL org.opencontainers.image.source="https://github.com/Filipe-Souza/rathena-docker"
LABEL org.opencontainers.image.description="Contains a rAthena compilation environment in Debian."

RUN apt-get update -y -qq && apt-get install git make libmariadb-dev-compat libmariadb-dev gcc g++ zlib1g-dev libpcre3-dev nano autoconf -y \
    && apt-get clean && rm -rf /var/lib/apt/lists/* && mkdir -p /app

WORKDIR /app

RUN git clone $RATHENA_SOURCE_REPO . && rm -rf .git

RUN ./configure "$CONFIGURE_FLAGS" && make server --jobs=$(nproc)

RUN chmod a+x login-server char-server map-server web-server

FROM debian:bookworm-20251117 AS release

LABEL org.opencontainers.image.source="https://github.com/mestre8d/rathena-docker"
LABEL org.opencontainers.image.description="Contains a rAthena release environment in Debian."

RUN adduser --home /opt/rathena --shell /bin/sh --disabled-password --uid 1000 rathena

RUN apt-get update -y \
    && apt-get install libmariadb-dev-compat libmariadb-dev zlib1g-dev libpcre3-dev -y -qq \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

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