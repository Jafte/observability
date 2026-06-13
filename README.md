# Observability Stack

Self-hosted observability для собственных проектов.

| Сервис | Назначение | Порт |
|---|---|---|
| [GlitchTip](https://glitchtip.com) | Error tracking (аналог Sentry) | `GLITCHTIP_HOST` |
| [Umami](https://umami.is) | Web analytics (аналог GA) | `UMAMI_HOST` |
| Caddy | Reverse proxy + автоматический HTTPS | 80, 443 |
| PostgreSQL 18 | Общая БД (схемы: glitchtip, umami) | — |
| Valkey 9 | Кэш и очередь задач для GlitchTip | — |

## Быстрый старт

```bash
cp .env.example .env
```

Заполнить `.env`:

```bash
# Пароль для PostgreSQL
POSTGRES_PASSWORD=...

# Секретный ключ GlitchTip
# openssl rand -hex 32
GLITCHTIP_SECRET_KEY=...

# Домены (без https://)
GLITCHTIP_HOST=errors.example.com
GLITCHTIP_DOMAIN=https://errors.example.com

# Секрет Umami
# openssl rand -hex 32
UMAMI_APP_SECRET=...
UMAMI_HOST=analytics.example.com
```

Запустить:

```bash
docker compose up -d
```

## После первого запуска

Создать admin-пользователя GlitchTip:

```bash
docker compose exec glitchtip ./manage.py createsuperuser
```

Umami при первом входе использует логин `admin` / пароль `umami` — **сменить сразу**.

## Email

По умолчанию письма выводятся в лог контейнера (`consolemail://`). Для настройки SMTP:

```env
EMAIL_URL=smtp://user:password@smtp.example.com:587?tls=true
DEFAULT_FROM_EMAIL=noreply@example.com
```

## Структура

```
.
├── docker-compose.yml
├── .env.example
└── docker/
    ├── caddy/
    │   └── Caddyfile
    └── postgres/
        └── init.sql   # создаёт БД glitchtip и umami
```
