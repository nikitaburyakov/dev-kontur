# Развертка разработческого контура 1С EDT
## 1. Инфраструктура
   Необходимые компоненты:
      - Платформа 1С: Предприятие
      - 1C: EDT
      - onescript (с библиотеками)
      - vscode
      - gitlab сервер
      - gitlab-runner (./gitlab-ce/docker-compose.yml)
      - nginx proxy manager (./nginx-pm/docker-compose.yml)
   Сервер разработки
     - Операционная система: MS Server 2019+
     - Установленные компоненты:
         - docker engine (Docker Desktop)
         - nginx pm
         - Платформа 1С: Предприятие
         - 1C: EDT
         - vscode
         - Сервер 1С (уточнение)
         - СУБД MS SQL (уточнение)
   Сервер автоматизации
     - Операционная система: Ubuntu 22.04 (Server, Desktop)
     - Установленные компоненты:
         - [docker engine](https://docs.docker.com/engine/install/)
         - [nginx pm](https://nginxproxymanager.com/setup/)
         - Платформа [1С: Предприятие](https://releases.1c.ru/project/Platform83)
         - [1C: EDT](https://releases.1c.ru/project/DevelopmentTools10)
         - [gitlab](https://docs.gitlab.com/install/package/) сервер
         - [gitlab-runner](https://docs.gitlab.com/runner/install/linux-repository/)
