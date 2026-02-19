Настройки из docker-compose.yml не переопределяют порты по умолчанию.
ВАЖНО!!! Это проблема скорее всех доступных docker образов с ftp сервером на борту.

Чтобы переопределить порты надо поправить конфигурационный файл внутри контейнера:

Заходим в контейнер:
docker exec -it ftp-server sh

Смотрим содержимое:
cat /etc/vsftpd.conf

Правим если надо:
- замена ip адреса на действительный:
	sed -i 's/pasv_address=0.0.0.0/pasv_address=10.13.8.11/' /etc/vsftpd.conf

- переопределение мин./макс. порта для пассивного режима
	sed -i 's/pasv_min_port=40000/pasv_min_port=5000/' /etc/vsftpd.conf
	sed -i 's/pasv_max_port=40009/pasv_max_port=5009/' /etc/vsftpd.conf
