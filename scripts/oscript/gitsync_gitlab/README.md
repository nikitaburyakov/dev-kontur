# Настройка интеграции хранилищ 1С и gitlab + sonar

## Настройка сервера хранилищ

1. Поднятие службы сервера хранилищ:
   * Linux: 
     > ./crserver -daemon -port 1542 -d <ПутьККаталогуХранилищ>
     Для регистрации сервиса создать .service для systemd
     ```
     [Unit]
     Description=1C:Enterprise CRS Server 8.3 (8.3.27.2074)
     [Service]
     ExecStart=/opt/1cv8/x86_64/8.3.27.2074/crserver -d /home/usr1cv8/.1cv8/1C/1cv8/repos
     RemainAfterExit=yes
     User=usr1cv8
     Group=grp1cv8
     [Install]
     WantedBy=multi-user.target
     ```
   * Windows
     ```
      crserver.exe -instsrvc -usr .\USR1CV8 -pwd password -start -port 1542 -d <ПутьККаталогуХранилищ>
     ``` 
    > ВНИМАНИЕ!!! Лучше переопределять порт сервера хранилищ, если планируется доступ через прокси-сервер, \
    > чтобы не было возможности подключиться по дефолтному порту
  
2. Настройка Веб-сервера (на примере Apache24):
   * Добавляем в apache2.conf:
     ``` LoadModule _1cws_module /opt/1cv8/x86_64/<ВерсияПлатформы>/wsap24.so
         Alias "/repos" "/var/1C/www/repos" # /repos - Адрес веб-ресурса, /var/1C/www/repos - путь до каталога с файлом *.1ccr
         <Directory "/var/1C/www/repos">
           AllowOverride None
           Options None
           Require all granted    
           SetHandler 1c-application
         </Directory>
     ```
     ВНИМАНИЕ!!! Не путать каталог с файлом *.1ccr и <ПутьККаталогуХранилищ>. \
     До каталога с *.1ccr должны быть права у пользователя Apache (напр. www-data),
     до каталога с хранилищами (напр. /var/1C/repos) должны быть права у пользователя службы crserver (обычно USR1CV8).

   * В каталоге для *.1ccr создаем файл (напр. repo.1ccr) с содержимым:
     ```
     <?xml version="1.0" encoding="UTF-8"?>
     <repository connectString="tcp://127.0.0.1"/> # адрес сервера хранилищ
     ```
   * Перезапускаем сервер apache
   * Доступ до хранилищ:
     ```
     tcp://<АдресСервера>/<ОтносительныйПуть> # напр.: tcp://localhost/project1/main
                                                       tcp://localhost/project1/ext_1
     http://<АдресСервера>/<ПутьДоФайла1CCR>/<ОтносительныйПуть> # напр.: http://localhost/repos/repo1.ccr/project1/main
                                                                          http://localhost/repos/repo1.ccr/project1/ext_1
     ```
     
## Настройка прокси-сервера хранилищ

1. Устанавливаем [OneScript](https://oscript.io/)
2. Устанавливаем библиотеку [oproxy](https://github.com/infina15/oproxy)
3. Создаем каталог, где будет храниться файл с проверками (ПроверкиПроксиСервера.os):
   ```
   cd D:\oproxy_catalog
   oproxy init
   ```
4. Создаем и запускаем прокси-сервер в режиме службы:
   ```
   oproxy create-daemon --proxy-port 2555 \                          # порт прокси-сервера
                        --storage-server localhost \                 # адрес сервера хранилищ
                        --storage-port 2542 \                        # порт сервера храниилищ
                        --check-file D:\...\ПроверкиПроксиСервера.os # каталог с проверками
   ```
   * Доступ до хранилищ:
     ```
     tcp://<АдресСервера>/<ОтносительныйПуть> # напр.: tcp://localhost:2555/project1/main  # через порт прокси-сервера
                                                       tcp://localhost:2555/project1/ext_1
     ```
## Настройка синхронизации хранилищ

1. Устанавливаем [gitsync](https://github.com/oscript-library/gitsync)
   ```
   opm install gitsync
   ```
2. Устанавливаем [gitsync-plugins](https://github.com/oscript-library/gitsync-plugins)
   ```
   opm install gitsync-plugins
   ```
3. Активируем плагины:
   ```
   gitsync p init
   gitsync enable sync-remote # install не обязательно
   gitsync enable increment
   ```
4. Создаем репозиторий в gitlab (с инициализацией)
5. Клонируем локально туда, где будет находиться рабочий каталог.
6. Инициализируем gitsync:
   ```
   gitsync init --storage-user ServiceUser       # имя пользователя хранилища
                --storage-pwd 123654             # пароль пользователя хранилища
                 {-e bit_ДоработкиАдаптация}     # имя расширения, если это расширение
                 tcp://localhost:2555/test/main  # адрес хранилища
                 E:\GIT\test\main                # каталог внутри репозитория
   ```
   gitsync создаст 2 файла внутри каталога:
   - AUTHORS - идентификация комиттеров по имени пользователя хранилищ
   - VERSION - хранит версию хранилища на момент последней синхронизации
7. Заполняем файл AUTHORS согласно рабочим данным:
   ```
   IIIvanov=Ivan Ivanov <iiivanov@1cbit.ru>
   ```
8. Настраиваем репозиторий:
   - добавляем ConfigDumpInfo.xml в .gitignore
   - включаем LFS в .gitattributes
   ```
    *.cf filter=lfs diff=lfs merge=lfs -text
    *.bin filter=lfs diff=lfs merge=lfs -text
    *.png filter=lfs diff=lfs merge=lfs -text
    *.gif filter=lfs diff=lfs merge=lfs -text
    *.bmp filter=lfs diff=lfs merge=lfs -text
    *.jpg filter=lfs diff=lfs merge=lfs -text
    *.zip filter=lfs diff=lfs merge=lfs -text
   ```
   - проверяем дополнительные настройки:
   ```
   core.longpaths=true                        #  поддержка длинных путей Windows
   filter.lfs.clean=git-lfs clean -- %f       # LFS 
   filter.lfs.smudge=git-lfs smudge -- %f     # LFS
   filter.lfs.process=git-lfs filter-process  # LFS
   filter.lfs.required=true                   # LFS
   safe.directory=*                           # если есть проблемы с доступом в каталоги git
   http.sslverify=false                       # для https и самоподписанных сертификатов
   http.postbuffer=1048576000                 # для передачи больших данных по сети
   ```

