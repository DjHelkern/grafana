# Monitoring Stack (Grafana + Prometheus + Exporters + Alertmanager)

Этот `docker-compose.yml` поднимает полный мониторинговый стек для наблюдения за инфраструктурой и сервисами.  
Включены Grafana, Prometheus, Node Exporter, cAdvisor, Alertmanager с Telegram-ботом и Blackbox Exporter.

---

## Сервисы

### 📊 Grafana
- Образ: `grafana/grafana:latest`
- Порт: `3000`
- Данные и конфиги сохраняются в volume’ах (`grafana-data`, `grafana-configs`).
- Интерфейс для построения дашбордов и визуализации метрик.

### 📈 Prometheus
- Образ: `prom/prometheus:latest`
- Порт: `9090`
- Хранит данные в volume `prom-data`.
- Конфигурация берётся из `prom-configs`.
- Собирает метрики от Node Exporter, cAdvisor, Blackbox и других экспортеров.
- Использует Alertmanager для уведомлений.

### 🐳 cAdvisor
- Образ: `gcr.io/cadvisor/cadvisor:latest`
- Порт: `8080`
- Снимает метрики использования ресурсов Docker-контейнеров (CPU, память, I/O).

### 🧰 Node Exporter
- Образ: `prom/node-exporter:latest`
- Порт: `9100`
- Снимает метрики с хоста (CPU, память, диски, файловая система, сеть).
- Пробрасываются системные директории (`/proc`, `/sys`, `/`).

### 🌐 Blackbox Exporter
- Образ: `prom/blackbox-exporter`
- Порт: `9115`
- Используется для проверки доступности сервисов (HTTP, HTTPS, TCP, ICMP).
- Конфигурация берётся из volume `blackbox`.

### 📢 Alertmanager
- Образ: `prom/alertmanager:latest`
- Порт: `9093`
- Отвечает за обработку алертов от Prometheus.
- Конфигурация хранится в volume `alertmanager`.

### 🤖 Alertmanager Bot
- Образ: `metalmatze/alertmanager-bot:latest`
- Порт: `8081`
- Подключается к Alertmanager и отправляет уведомления в Telegram.
- Требуется переменные окружения:
  - `TELEGRAM_ADMIN` — ID администратора
  - `TELEGRAM_TOKEN` — токен Telegram-бота
- Хранит данные в volume `alert-data`.

### 💾 Redis
- Образ: `redis:latest`
- Порт: `6379`
- Используется как вспомогательный сервис (например, для хранения временных данных).

---

## Установка и запуск

1. Скопируйте `docker-compose.yml` и `README.md` в рабочую директорию.
2. Создайте необходимые volumes (они будут созданы автоматически при запуске).
3. Запустите стек:
   ```bash
   docker-compose up -d

## Проверьте работу сервисов:

Grafana → http://localhost:3000
 (логин/пароль по умолчанию: admin / admin).

Prometheus → http://localhost:9090

Alertmanager → http://localhost:9093

Node Exporter → http://localhost:9100

cAdvisor → http://localhost:8080

Blackbox → http://localhost:9115

## Volumes

grafana-data — данные Grafana

grafana-configs — конфиги Grafana

prom-data — данные Prometheus

prom-configs — конфиги Prometheus

alert-data — данные Alertmanager Bot

alertmanager — конфиги и данные Alertmanager

blackbox — конфигурация Blackbox Exporter

## Примечания

Перед запуском настройте конфиги Prometheus (prometheus.yml), Alertmanager (config.yml) и Blackbox (blackbox.yml).

Для работы Telegram-бота нужно указать реальные TELEGRAM_ADMIN и TELEGRAM_TOKEN.

Пароль Grafana рекомендуется поменять после первого входа.
