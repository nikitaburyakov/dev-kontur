# Установка SonarQube

При возникновении ошибки
```
bootstrap check failure [1] of [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]; for more information see [https://www.elastic.co/guide/en/elasticsearch/reference/8.16/bootstrap-checks-max-map-count.html]
```
необходимо увеличить количество виртуальных областей памяти (vm.max_map_count):
```
# 1. Добавляем настройку в файл sysctl.conf
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf

# 2. Применяем изменения
sudo sysctl -p

# 3. Проверяем, что значение применилось
sysctl vm.max_map_count
```
Учетные данные по умолчанию:
   admin:admin

# Настройка интеграции GitLab и SonarQube

## Импорт самоподписанного сертификата GitLab:
   Для корректной работы интеграции при наличии у GitLab самоподписанного сертификата, необходимо импортировать сертификат в SonarQube:
   1. Выполняем экспорт сертификата из GitLab
     ```
     openssl s_client -connect <gitlab-hostname>:443 -showcerts </dev/null 2>/dev/null | \
     openssl x509 -outform PEM > gitlab-cert.pem
     ```
   2. Копируем сертификат в контейнер SonarQube:
     ```
     docker cp gitlab-cert.pem sonarqube-container:/tmp/gitlab-cert.pem
     ```
   3. Выполняем импорт в Java truststore
     ```
     docker exec --user=root sonarqube-container keytool -importcert -keystore \
                 /opt/java/openjdk/lib/security/cacerts -storepass changeit \
                 -alias gitlab -file /tmp/gitlab-cert.pem -noprompt
     ```
   * Перезапускаем SonarQube.

## Настройка интеграции с GitLab:

   1. Создаем отдельного пользователя для GitLab (напр. gitlab). \
      Получаем для учетной записи токен доступа, настраиваем права.
   2. На стороне GitLab создаем пользователя для SonarQube (напр. SonarQube) \
      Получаем для учетной записи токен доступа, настраиваем права.
      Необходимо зайти непосредственно в гитлаб от нового пользователя (чтобы корректно прошла интеграция из Sonarqube)
   4. Получаем для этих пользователей токены приложений (через настройки созданных учетных записей).
   5. Настраиваем интеграцию в SonarQube (Администрирование - Интеграция с ALM - GitLab):
      - указываем адрес хоста GitLab (напр. https://gitlab.example.com)
      - указываем токен доступа (из п.2)
      Проверяем конфигурацию: если конфигурация не верна, надо смотреть логи.
   6. Импортируем проект из GitLab в SonarQube (Проекты - Создать проект - Из GitLab).
   7. При импорте сохраняем токен доступа и ключ проекта, которые выдаст SonarQube
   8. В настройках проекта настраиваем:
      * плагин 1C BSL
      * профиль качества
      * порог качества
      * права для пользователя GitLab
   9. На стороне GitLab в импортированном проекте:
      * Настраиваем CI/CD - variables:
        ```
        SONAR_HOST_URL: # Адрес хоста SonarQube
        SONAR_TOKEN: Токен из п. 6
        ```
      * В корне репозитория создаем файл sonar-project.properties, где указываем ключ проекта из п. 6.
        Прим.:
        ```
        sonar.projectKey=firstbit_impex_133ed269-476b-4dfe-836a-f3aae9db3e1d                           # Ключ проекта из п. 6
        sonar.qualitygate.wait=true                                        
        sonar.inclusions=**/*.bsl,**/*.os                                                              # анализируем только код
        sonar.exclusions=**/*.gif, **/*.zip, **/*.svg, **/*.png, **/*.bmp, **/*.jpg, **/*.ico, **/*.cf # если нужен ParentConfigurations.bin для настройки пропуска файлов на поддержке
        sonar.sourceEncoding=UTF-8
        sonar.ssl.verify=false                                                                         # для доступа к GitLab по https с самоподписанным сертификатом
        sonar.qualitygate.timeout=1800                                                                 # для первой синхронизации типа ERP
        ```    
   10. Выполняем первую синхронизацию (из pipeline gitlab), будет выполнена инициализация ветки проекта в SonarQube, построен первый отчет.
> Для GitLab EE доступно декорирование MR GitLab из SonarQube. \
> Когда выполняется отправка изменений MR в SonarQube (pipeline), пользователь SonarQube в GitLab оставляет ревью на странице MR. \
> При исправлении и повторном запуске (pipeline), пользователь SonarQube в GitLab самостоятельно выполняет resolve open thread на странице MR. \
> Чтобы этот механизм работал корректно, требуется проверить настройки SonarQube:
> - Project - Settings - DevOps Platform Integration
> - Adminstration - General - Server base URL

## Настройка авторизации OAuth 2.0 через GitLab

1. На стороне GitLab создаем новое приложение:
   > Admin - Applications - Add new application
   > - Name: SonarQube Server
   > - Redirect URI: <URL_SonarQube>/oauth2/callback/gitlab. Напр.: https://sonarqube-instance.com/oauth2/callback/gitlab
2. Scopes:
   > - read_user
3. На стороне SonarQube:
   > Administration → Configuration → General Settings → Authentication → GitLab \
   > Создаем конфигурацию:
   > - Application ID из GitLab
   > - Secret из GitLab
   > - URL GitLab
   > - Allow users to sign up - V

## Включение git-lfs для [sonarsource/sonar-scanner-cli:latest](https://hub.docker.com/r/sonarsource/sonar-scanner-cli)

1. Собираем Dockerfile:
   ```
   FROM sonarsource/sonar-scanner-cli:latest
   USER root
   RUN yum install -y git-lfs
   USER scanner-cli
   ```
2. Билдим образ:
   ```
   docker build -t sonar-scanner-cli-lfs:latest .
   ```
3. Меняем в pipeline GitLab имя базового образа на ```sonar-scanner-cli-lfs:latest```
   
