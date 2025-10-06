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
## 3. Настройка GitLab

   ### Модель ветвления
   Используем ветку main для обновления PROD базы \
   Используем ветку develop для обновления STAGE базы
   - Необходимо выполнить защиту веток (main, develop для принятия изменений через merge-request)
   - Настроить основную ветку для PR develop
   - Создаем группу с именем организации.
   - Создаем проект под эту группу (главная ветка main)
   - Создаем ветку develop
   - Настраиваем правила веток.
   - От ветки develop создаем ветку init (для инициализации всего проекта + проверки работоспособности правил веток). Через данную ветку будут через PR внесены изменения в develop => main
   - Подготавливаем конфигурации в некоторой базе 1С для импорта в EDT.
   - Импортируем конфигурации в EDT, подключаем remote репозиторий, подключаем скрипты (./scripts), пушим в ветку init, через PR мержим в develop, main
   
   ### Конфигурация сборки (.gitlab-ci.yml):
     ```
     stages:          # List of stages for jobs, and their order of execution
       - build
      
     build-job:       # This job runs in the build stage, which runs first.
        stage: build
        script:
          - oscript ./scripts/edt_build.os
        artifacts:
          untracked: true
          when: on_success
          access: all
          expire_in: 30 days
          paths:
            - "artifacts/*.cf*"
     ```
   - Настраиваем переменные среды для проекта (либо в самом .gitlab-ci.yml, либо через определение в скрипте и указания в файле params.yml):
     - ONEC_EDTCLI_PATH: <полный путь для исполнителя 1cedtcli> (напр. /opt/1C/1CE/components/1c-edt-2024.2.6+7-x86_64/1cedtcli)
     - ONEC_VERSION:     <версия платформы 1С> (напр. 8.3.26.1656)
     - RELEASES_DIR:     <путь к каталогу для создания артефактов>  (напр. artifacts)

   ### Учет задач
   Используется стандартные обсуждения (issues). \
   Используется свой набор меток (К разработке, В разработке, На проверке и т.д. в зависимости от процесса). \
   На ишью назначается ответственный (может изменять статус задачи на дашборде). \
   Ответственный по задаче создает в задаче ветку по данной задаче, делает pull origin в EDT и извлекает новую ветку.  \
   После выполнения работ по задаче, ответственный выполняет push -u origin <имя ветки> в EDT, заходит в GitLab, создает PR в ветку develop. \
   Коммит по задаче обязательно должен указывать на открытый ишью (#<Номер ишью> <Внутренний номер задачи>). \
   Необходимо сохранить прослеживаемость доработок в соответствии с поставленными задачами.
   


