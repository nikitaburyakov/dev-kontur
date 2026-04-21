# Настройка GitLab

## Выпуск самоподписанного сертификата

1. Выбираем имя хоста инстанса (напр. https://gitlab.my.ru), корректируем docker-compose.yml, запускаем контейнер:
   ```
   cd /path/to/gitlab-ee
   docker compose up -d
   ```
2. Выпускаем самоподписанный сертификат:
   ```
   openssl req -x509 -nodes -days 1825 -newkey rsa:2048 \
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
3. Редактируем права на полученные файлы сертификата:
   ```
   chmod 644 <path-to-cert>/<gitlab-hostname>*
   ```
4. Помещаем файлы сертификата (.crt, .key) в каталог контейнера /etc/gitlab/ssl/
5. В файле /etc/gitlab/gitlab.rb отключаем Let’s Encrypt.
   ```
   letcencrypt['enable'] = false
   ```
   GitLab может сам себе выпустить сертификат и затереть тот, который сгенерирован при помощи OpenSSL.
6. Применяем изменения.
   ```
   gitlab-ctl reconfigure
   ```
## Настройка reverse-proxy

1. Импортируем сертификат в Nginx-PM:
   * Sertificates - Add Certificate - Custom Certificate
   * указываем имя сертификата (напр. userv.ru)
   * подцепляем <gitlab-hostname>.key, <gitlab-hostname>.crt
2. Настраиваем reverse-proxy для выбранного имени хоста (см. Nginx PM) // TODO
   * создаем новый proxy-host:
     > domain names: имя хоста \
     > scheme: https \
     > hostname: ip адрес машины с контейнером (напр 192.168.0.80) \
     > forward port: порт который проброшен на 443 порт контейнера (напр. 5443)
3. На машинах, с которых требуется доступ к GitLab по http необходимо прописать hosts:
   > Windows C:\Windows\System32\drivers\etc\hosts \
   > Linux /etc/hosts \
   >> добавляем запись вида '<ip хоста с контейнером gitlab>   <имя хоста gitlab>' \
   >> напр.: 10.13.8.11	gitlab.userv.ru

## Административные настройки GitLab

1. Кроме единственной административной учетной записи необходимо создать учетную запись с правами администратора для текущего администратора системы.
2. Создать группу, которая будет источником для проектов (напр. firstbit).
3. Создать ролевые группы (напр. developers, analytics).
4. Создать пользователей, распределить по группам, установив первичные роли.

## Настройки проекта

1. Настроить политики веток (ветка-по умолчанию, правила, защита) 
2. Настроить зеркалирование репозитория (это и бэкап и резервный канал)
