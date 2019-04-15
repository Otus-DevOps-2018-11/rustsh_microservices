# rustsh_microservices
rustsh microservices repository

## Домашнее задание № 14 (Технология контейнеризации. Введение в Docker)

Что сделано:
1. Настроена интеграция репозитория с Travis CI по аналогии с репозиторием infra, а также интеграция репозитория на GitHub и Travis CI с каналом в Slack.
2. Поднята виртуальная машина с Linux.
3. На ней установлен Docker, локальный пользователь добавлен в группу docker, чтобы не нужно было каждый раз вводить `sudo`.
4. Исследованы запуск и удаление контейнеров и образов.

## Домашнее задание № 15 (Docker-контейнеры)

Что сделано:
1. Создан новый проект в GCP, установлен GCloud SDK, проект инициализирован и к нему настроен доступ.
2. Установлен docker-machine.
3. Создан docker-хост, окружение переключено на него командой `eval $(docker-machine env docker-host)`.
4. Созданы файлы для формирования docker-образа, включая Dockerfile.
5. Командой `docker build -t reddit:latest .` создан образ приложения reddit.
6. Командой `docker run --name reddit -d --network=host reddit:latest` из нового образа создан и запущен контейнер на VM в GCP.
7. В GCP создано правило файервола для порта 9292, проверена работоспобность приложения reddit.
8. Зарегистрирована учётная запись в Docker Hub, созданный образ загружен в удалённый репозиторий, затем выгружен и проверен локально.

## Домашнее задание № 16 (Docker-образы. Микросервисы)

Что сделано:
1. Скачанный каталог reddit-microservices переименован в src, скачан образ MongoDB.
2. Внутри каждой подпапки src создан и оптимизирован Dockerfile и на его основе создан соответствующий образ каждого компонента.
3. Создана сеть для приложения, внутри неё запущены контейнеры из наших образов с сетевыми алиасами.
4. Создан docker volume.

## Домашнее задание № 17 (Docker: сети, docker-compose)

Что сделано:
1. Контейнеры запущены с разными сетевыми драйверами (none, host, bridge), исследована разница между ними.
2. Исследованы сетевые алиасы и включение контейнеров в несколько сетей.
3. Установлен Docker Compose, создан файл docker-compose.yml с описанием проекта.
4. В docker-compose.yml заданы несколько сетей с их настройками, алиасы, а также параметры, описанные в файле .env.
5. При помощи команды `docker-compose up -d` запущено приложение reddit, состоящее из нескольких компонент.

### Базовое имя проекта
Базовое имя проекта можно задать, используя ключ `-p` (`--project-name`) в команде `docker-compose`. Второй способ — задать это имя в переменной окружения `COMPOSE_PROJECT_NAME` в консоли либо в файле .env.

## Домашнее задание № 18 (Устройство Gitlab CI. Построение процесса непрерывной поставки)

Что сделано:
1. При помощи docker-machine создана VM для GitLab с установленным Docker:
	```bash
	docker-machine create --driver google \
	--google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts \
	--google-machine-type n1-standard-1 \
	--google-zone europe-west1-b \
	--google-disk-size 50 \
	--google-tags http-server,https-server \
	gitlab-ci 
	```
2. На VM созданы нужные папки, подготовлен файл docker-compose.tml, установлен Docker Compose, затем с его помощью установлен сам GitLab.
3. В GitLab'е создана группа 'homework', внутри которой создан проект 'example'.
4. Добавлен удалённый репозиторий 'gitlab', в котором задана ссылка на GitLab.
5. Создан файл .gitlab-ci.yml для опредения CI/CD пайплайна.
6. На VM запущен и зарегистрирован раннер.
7. В пайплайн добавлено тестирование приложение (и соответствующий скрипт), заданы окружения, новые этапы (staging и production), условия и ограничения, определены динамические окружения.

## Домашнее задание № 19 (Введение в мониторинг. Системы мониторинга)

Что сделано:
1. Созданы правила файервола для Prometheus и Puma, создан Docker хост в GCE и настроено локальное окружение на работу с ним.
2. Запущен контейнер с Prometheus, исследован его интерфейс.
3. Созданы Dockerfile для создания образа с Prometheus и файл конфигурации prometheus.yml.
4. Собраны образы сервисов приложения при помощи скриптов docker_build.sh.
5. В docker-compose.yml опредены новые сервисы (сам Prometheus и Node экспортер).
6. При помощи Docker Compose развернуто приложение с системой мониторинга; исследована работа системы мониторинга.
7. Созданные в ходе работы образы загружены на Docker Hub:
	- ui: https://hub.docker.com/r/rustsh/ui
	- comment: https://hub.docker.com/r/rustsh/comment
	- post: https://hub.docker.com/r/rustsh/post
	- prometheus: https://hub.docker.com/r/rustsh/prometheus

## Домашнее задание № 20 (Мониторинг приложения и инфраструктуры)

Что сделано:
1. Создан Docker хост в GCE и настроено локальное окружение на работу с ним, создано правило файервола для сервисов мониторинга, открыты нужные порты (8080, 3000, 9093).
2. Файл docker-compose.yml разделен на два — для самих приложений (docker-compose.yml) и для мониторинга (docker-compose-monitoring.yml).
3. Установлен и исследован cAdvisor — инструмент для мониторинга состояния docker-контейнеров:
	- сервис добавлен в docker-compose-monitoring.yml;
	- информация о сервисе добавлена в prometheus.yml.
4. Установлена и исследована Grafana — инструмент для визуализации метрик:
	- сервис добавлен в docker-compose-monitoring.yml;
	- в веб-интерфейсе задан источник данных, тип и параметры подключения;
	- загружен и установлен сторонний дашборд;
	- в конфигурацию Prometheus добавлена информацию о сервисе post, чтобы он начал собирать с него метрики;
	- созданы и сохранены собственные дашборды Grafana — в т. ч. для сбора бизнес-метрик.
5. Установлен и исследован Alertmanager — инструмент для оповещения:
	- создан новый Dockerfile для сборки образа alertmanager;
	- создан файл config.yml, в котором определена отправка нотификаций канал Slack, а также создана интеграция с этим каналом;
	- сервис добавлен в docker-compose-monitoring.yml;
	- создан файл alerts.yml, в котором определены условия, при которых алерт должен срабатывать и посылаться в Alertmanager. В Dockerfile для сборки образа Prometheus добавлена операция копирования данного файла в образ;
	- информация о правилах добавлена в prometheus.yml;
	- протестирована отправка оповещений в канал Slack.
6. Созданные в ходе работы образы загружены на Docker Hub: https://hub.docker.com/u/rustsh
