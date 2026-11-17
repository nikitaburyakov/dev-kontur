# Настройка gitlab-runner

## Установка gitlab-runner

1. Устанавливаем gitlab-runner на хосты, которые должны выполнять операции pipeline:
   > Windows: [установка gitlab-runner на Windows](https://docs.gitlab.com/runner/install/windows/) \
   > Linux: [установка gitlab-runner на Linux](https://docs.gitlab.com/runner/install/linux-repository/) \
   > Docker: ```docker compose up -d```
2. Готовим самоподписанный сертификат GitLab:
   > Формируем файл сертификата:
   > ```
   > openssl s_client -connect <gitlab-hostname>:443 -showcerts </dev/null 2>/dev/null | openssl x509 -outform PEM > gitlab.crt
   > ```
   > Кладем сертификат в каталог раннера (<Каталог раннера>\certs):
   > - docker/linux: /etc/gitlab-runner/certs
   > - windows: C:/Program Files/gitlab-runner/certs
3. Добавляем новый раннер в GitLab:
   > Admin - CI/CD - Runners - Create instance runner \
   > Указываем тэги, которые будет исполнять раннер (напр. sonar|gitsync|build|deploy) \
   > Копируем токен раннера, выданный GitLab
4. Регистрируем раннер:
   ```
   gitlab-runner register  --url <gitlab-hostname>  --token <runner-token> --tls-ca-file "/etc/gitlab-runner/certs/gitlab.crt"
   - указываем имя раннера (напр. docker-runner-1)
   - выбираем executor (напр. docker или shell)
   - если docker, выбираем базовый образ (напр. alpine:latest)
   ```
5. Проверяем раннер в GitLab.
6. Редактируем config.toml:
   - Пример для windows
     ```
      concurrent = 1
      check_interval = 0
      connection_max_age = "15m0s"
      shutdown_timeout = 0
      
      [session_server]
      session_timeout = 1800
      
      [[runners]]
      name = "windows-runner"
      url = "https://gitlab.userv.ru"
      id = 1
      token = "glrt-wsS5w_l4CIzAzFWtlU8dh286MQp0OjEKdTp5Cw.01.1207y0jvo"
      token_obtained_at = 2025-12-10T04:24:40Z
      token_expires_at = 0001-01-01T00:00:00Z
      executor = "shell"
      shell = "powershell"
      builds_dir = "E:\\GitLab-Runner\\builds"    
      cache_dir = "E:\\GitLab-Runner\\cache"      
      tls-ca-file = "C:\\GitLab-Runner\\gitlab.crt"
      [runners.cache]
        MaxUploadedArchiveSize = 0
        [runners.cache.s3]
        [runners.cache.gcs]
        [runners.cache.azure]
     ```
   - Пример для docker:
     ```
      concurrent = 1
      check_interval = 0
      shutdown_timeout = 0
      
      [session_server]
      session_timeout = 1800
      
      [[runners]]
      name = "docker-runner-1"
      url = "https://gitlab.userv.ru"
      id = 1
      token = "glrt-CCF7vzZ7Yg5oC-udU6Qs7m86MQp0OjEKdTp5Cw.01.120ony6d7"
      token_obtained_at = 2025-11-23T15:38:16Z
      token_expires_at = 0001-01-01T00:00:00Z
      tls-ca-file = "/etc/gitlab-runner/certs/gitlab.crt"
      executor = "docker"
      [runners.cache]
      MaxUploadedArchiveSize = 0
      [runners.cache.s3]
      [runners.cache.gcs]
      [runners.cache.azure]
      [runners.docker]
      tls_verify = false
      image = "alpine:latest"
      privileged = false
      disable_entrypoint_overwrite = false
      oom_kill_disable = false
      disable_cache = false
      volumes = ["/cache"]
      shm_size = 0
      network_mtu = 0
      volumes = ["/cache", "/var/run/docker.sock:/var/run/docker.sock"]
      extra_hosts = ["gitlab.userv.ru:10.13.8.11"] 
      dns = ["8.8.8.8", "8.8.4.4"]
     ```
  7. Перезапускаем gitlab-runner
