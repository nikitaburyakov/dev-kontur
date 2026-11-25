FROM ubuntu:22.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    locales \
    ca-certificates \
    libglib2.0-0 \
    libicu70 \
    && sed -i '/ru_RU.UTF-8/s/^# //g' /etc/locale.gen \
    && locale-gen \
    && rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=noninteractive \
    LANG=ru_RU.UTF-8 \
    LANGUAGE=ru_RU:ru \
    LC_ALL=ru_RU.UTF-8 \
    ONEC_HOME=/opt/1cv8/current \
    PATH="/opt/1cv8/current:${PATH}" \
    STORAGE_DIR=/usr/share/onec_storage \
    CRS_PORT=1542

COPY ./distr /tmp/distr

RUN find /tmp/distr/server* -type f -name "setup-full*.run" -exec chmod +x {} \; \
    -exec {} --mode unattended --enable-components config_storage_server,ru \;   \
    && ln -sfn /opt/1cv8/x86_64/8.3.* "$ONEC_HOME"

EXPOSE ${CRS_PORT}

CMD [ "sh","-c", "exec ${ONEC_HOME}/crserver -d ${STORAGE_DIR} -port ${CRS_PORT}"]