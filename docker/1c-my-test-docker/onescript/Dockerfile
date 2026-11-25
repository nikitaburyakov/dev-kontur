FROM ubuntu:22.04

ARG OSCRIPT_VERSION=2.2.0
ARG OPM_PACKAGES=""
ENV OSCRIPT_HOME="/opt/oscript-${OSCRIPT_VERSION}"
ENV PATH="${OSCRIPT_HOME}/bin:${PATH}"

RUN apt update \
    && apt install -y --no-install-recommends \
        locales ca-certificates gnupg dirmngr apt-transport-https software-properties-common curl wget unzip \
        mono-runtime libmono-system-web4.0-cil libmono-i18n4.0-all ca-certificates-mono \
    && locale-gen ru_RU.UTF-8 \
    && update-locale LANG=ru_RU.UTF-8 LC_ALL=ru_RU.UTF-8

ENV LANG=ru_RU.UTF-8
ENV LC_ALL=ru_RU.UTF-8

RUN case "$OSCRIPT_VERSION" in \
        1.*) \
            wget -q -O /tmp/onescript.deb "https://github.com/EvilBeaver/OneScript/releases/download/v${OSCRIPT_VERSION}/onescript-engine_${OSCRIPT_VERSION}_all.deb" \
            && dpkg -i /tmp/onescript.deb || apt-get install -f -y \
            ;; \
        2.*) \
            wget -q -O /tmp/oscript.zip  "https://github.com/EvilBeaver/OneScript/releases/download/v${OSCRIPT_VERSION}/OneScript-${OSCRIPT_VERSION}-linux-x64.zip" \
            && unzip -q /tmp/oscript.zip -d "$OSCRIPT_HOME" \
            && chmod +x "${OSCRIPT_HOME}/bin/"* \
            ;; \
    esac \
    && if [ -n "$OPM_PACKAGES" ]; then \
        for pkg in $OPM_PACKAGES; do \
            opm install "$pkg"; \
        done; \
    fi \
    && rm -rf /tmp/* /var/lib/apt/lists/*

ENV PATH="/opt/oscript-$OSCRIPT_VERSION:${PATH}" 

CMD ["oscript"]
