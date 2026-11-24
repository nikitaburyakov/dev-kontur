FROM ubuntu:22.04

ARG OSCRIPT_VERSION=2.2.0
ARG OPM_PACKAGES=""
ENV OSCRIPT_HOME="/opt/oscript-${OSCRIPT_VERSION}"
ENV PATH="${OSCRIPT_HOME}/bin:${PATH}"

RUN apt update \
    && apt install -y --no-install-recommends \
        locales ca-certificates gnupg dirmngr apt-transport-https software-properties-common curl wget unzip\
    && locale-gen ru_RU.UTF-8 \
    && update-locale LANG=ru_RU.UTF-8 LC_ALL=ru_RU.UTF-8 \
    && export LANG=ru_RU.UTF-8 \
    && export LC_ALL=ru_RU.UTF-8 \
    && wget -P /tmp https://github.com/EvilBeaver/OneScript/releases/download/v$OSCRIPT_VERSION/OneScript-$OSCRIPT_VERSION-linux-x64.zip \
    && unzip /tmp/OneScript-$OSCRIPT_VERSION-linux-x64.zip -d /opt/oscript-$OSCRIPT_VERSION\
    && chmod +x "${OSCRIPT_HOME}/bin/"* \
    && if [ -n "$OPM_PACKAGES" ]; then \
        for pkg in $OPM_PACKAGES; do \
            opm install "$pkg"; \
        done; \
    fi \
    && rm -rf /tmp/* /var/lib/apt/lists/*

ENV PATH="/opt/oscript-$OSCRIPT_VERSION:${PATH}" 

CMD ["oscript"]
