[Источник onec-docker на github](https://github.com/firstBitMarksistskaya/onec-docker)

## Сборка образа:

DOCKER_REGISTRY_URL - оставляем пустым для dockerhub
oscript:2.2.1 - версия oscript  в канале stable

```
docker build --build-arg DOCKER_REGISTRY_URL="" \
  -t docker-registry.nikserv.ru/oscript:2.2.1 \
  -f oscript/Dockerfile .
```

## Проверка

```
docker run --rm -it docker-registry.nikserv.ru/oscript:2.2.1  bash 

oscript -v
```

## Cборка образа с библиотеками на примере yard

docker build \
  --build-arg DOCKER_REGISTRY_URL=${DOCKER_REGISTRY_URL} \
  -t ${DOCKER_REGISTRY_URL}/oscript-utils:2.2.1 \
  -f oscript-utils/Dockerfile .

### Комментарии
Проверено на OneScript 2.2.1 (канал stable в Dockerfile)