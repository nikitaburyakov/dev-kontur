ARG DOCKER_REGISTRY_URL
ARG DOCKER_IMAGE=oscript
ARG DOCKER_TAG=stable

FROM ${DOCKER_IMAGE}:${DOCKER_TAG} AS base

ENV OPROXY_PORT=1542
ENV STORAGE_PORT=1542
ENV OPROXY_CHECKFILE=/tmp/oproxy_data/ПроверкиПроксиСервера.os

RUN opm install oproxy \
    && mkdir -p /tmp/oproxy_data \
    && cd /tmp/oproxy_data \
    && oproxy init 

EXPOSE 1542

CMD ["sh", "-c", "exec oproxy start --proxy-port ${OPROXY_PORT} --storage-server ${STORAGE_HOST} --storage-port ${STORAGE_PORT} --check-file ${OPROXY_CHECKFILE}"]
