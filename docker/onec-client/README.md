
[Источник onec-docker на github](https://github.com/firstBitMarksistskaya/onec-docker)

Для сборки образа требуется либо собрать на базе downloader - образ с установленным onescript и библиотекой yard, либо положить дистрибутив в каталог distr.

## Собираем образ onescript

см. [Сборка образа onescript](https://github.com/nikitaburyakov/dev-kontur/tree/main/docker/onescript)

## Собираем образ onescript-utils

см. [Сборка образа onescript](https://github.com/nikitaburyakov/dev-kontur/tree/main/docker/onescript)

## Собираем сам образ клиента

 docker build \
  --build-arg DOCKER_REGISTRY_URL=${DOCKER_REGISTRY_URL} \
  --build-arg BASE_IMAGE=oscript-utils \
  --build-arg BASE_TAG=2.2.1 \
  --build-arg ONEC_USERNAME=${ONEC_USERNAME} \
  --build-arg ONEC_PASSWORD=${ONEC_PASSWORD} \
  --build-arg ONEC_VERSION=${ONEC_VERSION} \
  -t ${DOCKER_REGISTRY_URL}/onec-client:${ONEC_VERSION} \
  -f client/Dockerfile .

