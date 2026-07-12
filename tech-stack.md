# Технологический стек (базовый каркас)

**Статус:** инвентаризация репозитория «пустого приложения» (базовый набор компании).
**Репозиторий / источник:** `git@gitlab.apnethq.com:apnet-internal/symfony-project.git` (локально: `sf-project5`), ветка `master`, тег/версия `0.0.10-alpha`.
**Дата описания:** 2026-07-12

## 1. Назначение каркаса

Повторяемая основа backend на **Symfony**: единое локальное окружение в Docker, согласованный цикл разработки и QA (`Makefile`, Phing, PHP CS Fixer / PHPStan / Rector / PHPUnit / Behat), деплой через **GitLab CI/CD** на удалённый Docker Compose без расхождения «на хосте работает / в CI иначе».

**Готово к разработке продукта:** PHP-приложение с REST API (JWT), админкой (Sonata), MySQL + Redis (сессии), nginx, секреты и prod-образы, пайплайн build → test → deploy для mainline-веток.

**Намеренно пустое / заглушка:** доменная бизнес-логика и продуктовые сценарии в `ABOUT.md` (разделы без префикса «Шаблон:»); отдельный фронтенд-продукт в этот репозиторий обычно не кладут.

Типичный состав продукта на базе каркаса (**со слов Пользователя**): API, админка, очереди Messenger (часто добавляют), БД; фронтенд — вне репозитория backend.

## 2. Сводка стека

| Слой | Что используется в каркасе | Примечание |
|------|----------------------------|------------|
| Язык / runtime | PHP **8.4** (platform в Composer: `8.4.21`), php-fpm 8.4 | Образ на `debian:trixie-slim` + Sury PHP; расшир.: curl, gd, intl, mbstring, mysql, redis, xml, zip |
| Веб-фреймворк / HTTP | **Symfony 7.4.*** | Flex; FOSRestBundle, Lexik JWT, Nelmio ApiDoc, Security, Twig |
| Фронтенд (если есть) | В каркасе есть задел `assets/` + StimulusBundle; **со слов Пользователя** каталог `assets/` лишний | Обычно UI вне репо; в репо — Twig; статика (js/css) кладётся в образ nginx (`Dockerfile-nginx`, `COPY … /srv/public/`); сборка статики — ответственность разработчика продукта |
| СУБД / хранилища | **MySQL 8.0** (`mysql:8.0-debian`), **Redis 8** (`redis:8-trixie`) | Schema: `doctrine:schema:update` в Phing; миграции — в основном для **данных**. Redis — сессии (`RedisSessionHandler`) |
| Очереди / кэш / прочее | Messenger / Mailer **в каркасе нет** | Часто добавляют при старте продукта (**со слов Пользователя**) |
| Reverse proxy / TLS | Внутри стека: **nginx** (контейнер, порт 80). TLS и внешний proxy — **снаружи** | На сервере всегда внешний reverse proxy; label `docker-gen.host: ${SERVER_NAME}` → сертификат **Let's Encrypt** (**со слов Пользователя**) |
| Контейнеризация | Docker, Docker Compose | Dev: `docker-compose.yml` + override; prod: `docker-compose.deploy.yml` → отрендеренный `build/docker-compose.yml` |
| Сборка зависимостей | **Composer**; Phing 3.1.2 в образе | `make deps` / `composer install` только в контейнере `php` |
| CI/CD | **GitLab CI** (`.gitlab-ci.yml`), image `docker:26`, tags `executor-docker` + `docker-in-docker` | Стадии: `.pre` → `build` → `test` → `deploy` → `.post` |
| Наблюдаемость / безопасность | Hardening контейнеров (см. §7); Grafana/Prometheus/Loki **в каркасе нет** | Часто добавляют руками (**со слов Пользователя**) |

## 3. Модули и пакеты каркаса

Всё ниже **включено по умолчанию**; отдельного механизма profiles / optional packages нет — отключение = правка кода/compose/composer вручную (**со слов Пользователя**).

| Идентификатор / путь | Назначение | По умолчанию | Как включается / отключается |
|----------------------|------------|--------------|------------------------------|
| `src/` + Symfony Kernel | Приложение: сущности User/Client/Config/UserProvider, REST, Admin, команды | вкл | Ядро продукта |
| FOSRest + Lexik JWT + Nelmio ApiDoc | REST API, JWT-аутентификация, OpenAPI | вкл | Бандлы в `composer.json` / `config/packages/` |
| Sonata Admin (+ ORM Admin, Block, Form, …) | Админ-CRUD | вкл | Бандлы + `src/Admin/` |
| Doctrine ORM + Migrations + Gedmo / beberlei extensions | БД, схема, data-миграции | вкл | MySQL обязателен для типового сценария |
| Redis (сессии) | Хранение сессий Symfony | вкл | `REDIS_URL`, сервис `redis` в compose |
| nginx + php-fpm | HTTP → FastCGI | вкл | Dev/prod Dockerfiles |
| `spare` (только deploy) | Второй php-fpm: миграции при деплое + обслуживание запросов (upstream вместе с `php`) | вкл на prod | `docker-compose.deploy.yml`; upstream: `docker/nginx/conf.d/upstream.conf` |
| phpMyAdmin | UI к MySQL | вкл **только local** | Сервис в `docker-compose.yml` (в deploy нет) |
| Phing (`build.xml`, `tools/`) | init, database-deploy, app-deploy, секреты | вкл | `make init`, CI deploy |
| QA: CS Fixer, PHPStan, Rector, PHPUnit, Behat | Качество и регрессия | вкл | `make qa`; jobs `qa:*` в CI |
| `assets/` + StimulusBundle | Заготовка UX Stimulus | формально вкл | **Со слов Пользователя:** лишнее; обычно не используют |
| Cursor (`.cursor/`) | Правила/агенты/скиллы для разработки с AI | вкл в репо | Не влияет на runtime продукта |
| Mailer / Messenger / observability | — | **нет в каркасе** | Часто добавляют при продукте (**со слов Пользователя**) |

**Зависимости модулей (кратко):**

- **JWT / REST** — ключи PEM + `ENV_jwt_passphrase`; без них API-auth и prod-деплой mainline не сойдутся.
- **Sonata** — Twig, Security, Doctrine; убрать = потерять админку каркаса.
- **Redis** — сессии; убрать потребует смены `handler_id` и сервиса compose.
- **`spare`** — уменьшает downtime при деплое (миграции на spare до пересоздания `php`) и **также** обрабатывает пользовательские запросы через nginx upstream (**со слов Пользователя** + `upstream.conf`).
- **phpMyAdmin** — только удобство local; на prod отсутствует.

**Избыточность / вычищение:** единой статистики «что чаще выкидывают» нет — каждый продукт по-разному (**со слов Пользователя**).

**Часто не хватает и добавляют руками (**со слов Пользователя**):**

- работа с электронной почтой (Mailer / аналог) — почти всегда;
- Symfony Messenger — часто;
- Grafana / Prometheus / Loki — часто.

## 4. Локальная разработка

Подъём (см. также `README.md`, `docs/symfony/init.md`):

```bash
cp docker-compose.override.yml.dist docker-compose.override.yml
# в override: user "<uid>:<gid>" (id -u / id -g)
make up
make init    # phing: БД + schema + migrations
make deps    # при необходимости composer install
```

**Сервисы (dev):**

| Сервис | Порт на хосте | Назначение |
|--------|---------------|------------|
| nginx | 80 | HTTP |
| php | — (внутр. 9000) | приложение, Composer, QA |
| mysql | 3306 | БД |
| redis | — | сессии |
| phpmyadmin | 8080 | UI БД |

**Переменные (имена, не значения):** в `.env` — `ENV_app_secret`, `ENV_database_*`, `ENV_jwt_passphrase`, `ENV_redis_*`, `ENV_default_uri`; производные `APP_SECRET`, `DATABASE_URL`, `REDIS_URL`, `JWT_*`. Production-секреты в git не хранят.

**Проверка «каркас жив»:** `docker compose ps`; открыть `http://localhost` (Index); при необходимости admin/API; `make qa` перед merge.

PHP/Composer/QA — **только** в контейнере `php` (`Makefile` или `docker compose exec php …`).

## 5. Окружения и деплой

| Окружение | Как попадает код | Куда выкладывается | Особенности |
|-----------|------------------|--------------------|-------------|
| local | clone + `make up/init` | Docker Compose на машине разработчика | override, phpMyAdmin, Xdebug в dist-override |
| **staging** | push в ветку **`master`** | хост из CI Variables окружения `master` | GitLab environment = `$CI_COMMIT_REF_SLUG`; конфиг Variables **отдельный** от prod (**со слов Пользователя**) |
| **production** | push в ветку **`production`** | хост из CI Variables окружения `production` | то же; другой набор Variables |

Подробный порядок: `DEPLOYMENT.md`, `.gitlab-ci.yml`.

**Вне репозитория (**со слов Пользователя**):**

- выбор конкретного сервера и мощностей — человек + CI Variables (`DEPLOY_HOST`, `DEPLOY_USER`, `DEPLOY_DIRECTORY`, `SERVER_NAME`, …);
- внешний reverse proxy + Let's Encrypt по метке `docker-gen.host`;
- `DEPLOY_USER` в группе `docker`, sudo NOPASSWD для `chgrp` (gid 33 / 999) — см. `DEPLOYMENT.md`.

**Ограничения каркаса:** приложение и зависимости в контейнерах; `read_only` FS + volumes для постоянного состояния (`docs/symfony/volume.md`); PHP-инструменты не запускают на хосте.

## 6. CI/CD

**Файл:** [`.gitlab-ci.yml`](.gitlab-ci.yml). Описание для людей: [`DEPLOYMENT.md`](DEPLOYMENT.md).

**Основные стадии / jobs:**

- **`.pre`:** `prepare-mainline:env` — проверка CI Variables / File variables, артефакт `.env.prod.local` (только mainline).
- **`build`:** source / app (`prod`) / nginx images → GitLab Container Registry (mainline: полный набор; feature-ветки: source для тестов).
- **`test`:** `prepare:composer-cache`, `qa:php-cs-fixer`, `qa:phpstan`, `qa:rector`, `qa:phpunit`, `qa:behat` (+ MySQL/Redis services), на mainline ещё `qa:tools`.
- **`deploy` (mainline):** prepare compose, prepare secrets, git-image для SSH, `secure-copy` → на сервере pull, spare + `phing database-deploy`, recreate php, restart nginx.
- **`.post`:** cleanup source image tag (не-mainline, `allow_failure`).

**Зелёный пайплайн на «пустом» приложении:** успешные QA jobs; для mainline — ещё заданные Variables и File variables, успешный deploy на настроенный хост.

**Секреты / переменные CI (имена):**  
`SERVER_NAME`, `DEPLOY_USER`, `DEPLOY_HOST`, `DEPLOY_DIRECTORY`, `ENV_app_secret`, `ENV_database_password`, `ENV_database_root_password`, `ENV_jwt_passphrase`, `ENV_redis_password`; File: `JWT_PRIVATE_KEY`, `JWT_PUBLIC_KEY`, `SSH_PRIVATE_KEY`. Registry: `CI_JOB_TOKEN` / `CI_REGISTRY*`. Образы: `CI_APP_IMAGE_WITH_TAG`, `CI_NGINX_IMAGE_WITH_TAG`, `CI_SOURCE_IMAGE_*`, `CI_GIT_IMAGE_*`.

## 7. Безопасность и поставка контейнеров

Уже заложено в compose (dev и deploy):

- `read_only: true` у nginx / php (/ spare);
- `cap_drop: [ALL]`, `security_opt: no-new-privileges:true`;
- процессы не от root: PHP `www-data`, nginx `nginx`, redis `999:999`;
- секреты prod через Docker secrets + config-шим `secrets/env.php`; dotenv и JWT не копируются в prod-слой приложения (`Dockerfile` `COPY --exclude=…`);
- отдельного Trivy / hardened base images в текущем каркасе **нет** (в инвентаризации не фиксируем планы вне репо).

## 8. Точки расширения под новый продукт

| Место | Обычно сюда |
|-------|-------------|
| `src/Entity`, `Repository`, `Admin`, `Controller` (+ Rest), `Command`, `Manager` | доменная логика, API, админка |
| `migrations/` | изменения **данных** (схема — через schema update в Phing) |
| `config/packages/`, `config/routes/` | бандлы, security, REST |
| `templates/` | Twig (админка и серверные страницы) |
| `public/` + копирование в nginx-образ | статика для отдачи nginx |
| `features/`, `tests/` | Behat / PHPUnit |
| `build.xml`, `secrets/*.twig` | init/deploy и генерация секретов |
| `.gitlab-ci.yml` / CI Variables | окружения staging/prod |

**Нельзя ломать без замены эквивалента**, если нужен тот же CI/CD и деплой: контракт `ENV_*` + dump-env, Docker secrets/configs layout, jobs mainline deploy, Phing `database-deploy`, upstream `php`+`spare`, `read_only`+volume-рецепт, QA gate перед deploy.

## 9. Известные пробелы и развилки

- Нет compose-profiles / optional packages — состав режут вручную.
- `assets/` + Stimulus в репо есть, но по практике компании считаются лишними (**со слов Пользователя**); фронт обычно отдельно.
- Mailer, Messenger, Grafana/Prometheus/Loki — типичные добавления, в каркасе отсутствуют.
- Выбор сервера, домена (`SERVER_NAME`), подмножества модулей — всегда решение человека при старте продукта.
- В deploy у `nginx` `depends_on` mysql/redis, у `php`/`spare` — нет симметрии с dev (факт compose; влияние на cold start — на усмотрение эксплуатации).
- Внешний TLS/proxy и Let's Encrypt — инфраструктура компании, не код приложения.

## 10. Краткая шпаргалка для передачи дальше

Каркас даёт Symfony 7.4 / PHP 8.4 backend в Docker: MySQL, Redis-сессии, nginx, REST+JWT, Sonata Admin, Phing, полный QA и GitLab deploy на Docker Compose с hardened-контейнерами.  
Типичный продукт: API + админка + БД (+ часто Mailer/Messenger и observability); фронтенд — отдельный репозиторий; статика при необходимости — в образ nginx.  
Локально: `override` → `make up` → `make init`.  
Деплой: ветка **`master` → staging**, ветка **`production` → prod**; конфиг GitLab Variables разный; на сервере внешний reverse proxy и LE по `docker-gen.host`.  
Сервер и мощности выбирает человек вне репо.  
Секреты — CI Variables / File variables и Docker secrets на хосте, не в git.
)
