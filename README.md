# Развертка разработческого контура 1С EDT
## 1. Инфраструктура
   - <b>Необходимые компоненты</b>:
      - Платформа 1С: Предприятие
      - 1C: EDT
      - onescript (с библиотеками)
      - vscode
      - gitlab сервер
      - gitlab-runner (./gitlab-ce/docker-compose.yml)
      - nginx proxy manager (./nginx-pm/docker-compose.yml)
   - <b>Сервер разработки</b>
     - Операционная система: MS Server 2019+
     - Установленные компоненты:
         - docker engine (Docker Desktop)
         - nginx pm
         - Платформа 1С: Предприятие
         - 1C: EDT
         - vscode
         - Сервер 1С (уточнение)
         - СУБД MS SQL (уточнение)
   - <b>Сервер автоматизации</b>
     - Операционная система: Ubuntu 22.04 (Server, Desktop)
     - Установленные компоненты:
         - [docker engine](https://docs.docker.com/engine/install/)
         - [nginx pm](https://nginxproxymanager.com/setup/)
         - Платформа [1С: Предприятие](https://releases.1c.ru/project/Platform83)
         - [1C: EDT](https://releases.1c.ru/project/DevelopmentTools10)
         - [gitlab](https://docs.gitlab.com/install/package/) сервер
         - [gitlab-runner](https://docs.gitlab.com/runner/install/linux-repository/)

## 2. Настройка инфраструктуры
   - Настройка сети:
     Суть задачи - обеспечить доступ к приложениям по DNS именам
     > Прим.: \
     > Сервер разработки имеет имя dev-1c, \
     > Сервер автоматизации имеет имя dev-aut
     >
     > Пропишем в /etc/hosts каждого из серверов привязку <ip хоста> <имя хоста>
     > 
     > DNS имя экземпляра gitlab имеет вид git.dev.ru \
     > На обоих серверах поднят nginx-pm и прослушивает стандартные порты 80 и 443.
     >
     > Чтобы получить доступ к gitlab по DNS (git.dev.ru), настроим маршрутизацию на обоих хостах:
     > - пропишем в /etc/hosts ip хоста и его DNS ```127.0.0.1 git.dev.ru```
     > - пропишем в nginx-pm в proxy-hosts новую запись
     >   - Domain Names: git.dev.ru
     >   - Forward IP: <ip-хоста сервера автоматизации>
     >   - Forward Port: <порт gitlab>
     >

