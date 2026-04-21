# Настройка GitLab

1. Выбираем имя хоста инстанса (напр. https://gitlab.my.ru), корректируем docker-compose.yml, запускаем контейнер:
   ```
   cd /path/to/gitlab-ee
   docker compose up -d
   ```
2. Настраиваем reverse-proxy для выбранного имени хоста (см. Nginx PM) // TODO
3. Выпускаем самоподписанный сертификат:
   ```
   sudo openssl req -x509 -nodes -days 1825 -newkey rsa:2048 \
    -subj "/C=RU/ST=MO/L=MO/O=EXP/OU=EXP/CN=<gitlab-hostname>" \
    -addext "subjectAltName = DNS:<gitlab-hostname>, IP:<gitlab-hostip>" \
    -keyout <path-to-cert>/<gitlab-hostname>.key \
    -out <path-to-cert>/<gitlab-hostname>.crt
   ```
   где:
    * <gitlab-hostname> - имя хоста gitlab, напр. gitlab.example.com
    * <gitlab-hostip> - ip хоста, на котором стоит gitlab (ip докер контейнера)
    * <path-to-cert> - путь, куда класть сертификаты (можно не указывать - в текущий каталог)
    * <user> - имя пользователя, в чей каталог будут сброшены сертификаты
4. Редактируем права на полученные файлы сертификата:
   ```
   chmod 644 <path-to-cert>/<gitlab-hostname>*
   ```
5. Помещаем файлы сертификата (.crt, .key) в каталог контейнера /etc/gitlab/ssl/
6. В файле /etc/gitlab/gitlab.rb отключаем Let’s Encrypt.
   ```
   letcencrypt['enable'] = false
   ```
   GitLab может сам себе выпустить сертификат и затереть тот, который сгенерирован при помощи OpenSSL.
7. Применяем изменения.
   ```
   sudo gitlab-ctl reconfigure
   ```
