[Источник onec-docker на github](https://github.com/firstBitMarksistskaya/onec-docker)

## Сборка образа:

ONEC_USERNAME - логин ИТС
ONEC_PASSWORD - пароль ИТС
EDT_VERSION   - версия EDT
DOWNLOADER_REGISTRY_URL - адрес docker-registry (без костылей - по https)
BASE_TAG      - версия JAVA для версии EDT

```
docker build \                                                  
    --build-arg ONEC_USERNAME=${ONEC_USERNAME} \
    --build-arg ONEC_PASSWORD=${ONEC_PASSWORD} \
    --build-arg EDT_VERSION=2026.2.2 \
    --build-arg BASE_IMAGE=eclipse-temurin \
    --build-arg BASE_TAG=25-jdk-jammy \
    --build-arg DOWNLOADER_REGISTRY_URL=docker-registry.nikserv.ru \
    --build-arg DOWNLOADER_IMAGE=oscript-downloader \
    --build-arg DOWNLOADER_TAG=latest \
    -t docker-registry.nikserv.ru/edt:2026.2.2 \
    -f edt/Dockerfile .
```

## Проверка

```
docker run --rm -it docker-registry.nikserv.ru/edt:2026.2.2 bash

1cedtcli --version
```

### Комментарии
Проверено на 1С EDT: 2026.2.2