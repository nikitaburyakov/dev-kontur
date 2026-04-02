## Настройка сбора данных

1. Поднимаем prometheus + grafana
2. Заходим в grafana http://localhost:3000
3. В Data Sources добавляем prometheus с URL http://localhost:9090
4. Нажимаем Save & Test
5. Выбираем дашборд https://grafana.com/grafana/dashboards/
   - PostgreSQL (ID=9628)
   - ubuntu-node (ID=1860)
7. В Grafana нажимаем на иконку плюса (Create) → Import. Вставляем ID дашборда и выбираем источник данных Prometheus.

### node-exporte: Экспорт счетчиков Linux (CPU, RAM, Disk, Network)
- доступен на порту 9100

### postgres-exporter: Экспорт показателей PostgreSQL
- доступен на порту 9187

Таблица портов экспортеров:
| Экспортер | Стандартный порт | Где используется |
| :--- | :--- | :--- |
| Node Exporter (Linux) | 9100 | Linux |
| Windows Exporter | 9182 | Windows |
| PostgreSQL Exporter | 9187 | PostgreSQL |
| MySQL Exporter | 9104 | MySQL |
| Redis Exporter | 9121 | Redis |
| Blackbox Exporter | 9115 | HTTP/ICMP |
| cAdvisor | 8080 | Docker контейнеры |
