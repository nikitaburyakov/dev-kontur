## Сборка

Пример сборки:
* Версия OneScript: `2.2.0`
* Пакеты к установке: `yard irac yadisk`
* Имя образа: `oscript`
* Тэг: `test`
```
docker build --build-arg OSCRIPT_VERSION=2.2.0 --build-arg OPM_PACKAGES="yard irac yadisk" -t oscript:2.2.0 .
```