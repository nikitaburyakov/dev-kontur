## Добавление аутентификации в GitLab

* Получить строку Base64 
```
echo -n "$LOGIN:$PASSWORD" | base64
```
* Подставить полученную строку в контекст JSON
```
{"auths":{"$DOCKER_REGISTRY_URL":{"auth":"$STRING_BASE64"}}}

```
* Добавить переменную "DOCKER_AUTH_CONFIG" в Variables проекта и указать в нее полученную строку JSON