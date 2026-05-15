# wg-easy-custom

> 🇷🇺 [Русская версия](#русская-версия) ниже / Russian version below

---

## English

A fork of [wg-easy](https://github.com/wg-easy/wg-easy) **v14** with added **client expiry management** — set a lifetime for each WireGuard client directly from the web UI.

Works over **HTTP without a domain** (unlike v15 which requires HTTPS), making it suitable for deployment on any VPS by IP address.

### What's new compared to original wg-easy

| Feature | Original | This fork |
|---|---|---|
| Client expiry | ❌ | ✅ |
| Auto-disable on expiry | ❌ | ✅ |
| Extend / reset expiry via UI | ❌ | ✅ |
| HTTP without domain | ✅ | ✅ |
| i18n (20 languages) | ✅ | ✅ (expiry UI translated) |

### Expiry features in detail

- **Set expiry on creation** — enter days / hours / minutes (leave empty for unlimited ∞)
- **Expiry badge** on each client card:
  - ⏳ `3 d. 2 h.` — active, time remaining
  - ⏳ `45 min.` — less than an hour left (yellow)
  - ⛔ `Expired` — client is disabled (row dimmed)
  - `∞` — unlimited
- **Auto-disable** — expired clients are disabled automatically every minute
- **Extend** — calendar button opens a modal: add days / hours / minutes from now; auto-enables the client
- **⛔ Reset time** — clears expiry and disables the client (revoke access)
- **∞ Make permanent** — clears expiry, client keeps running
- **Manual toggle behavior**:
  - Manually enabling an expired client → client becomes permanent (∞)
  - Manually disabling an active client → timer keeps running independently

### Quick start with Docker Hub

The easiest way — pull the pre-built image:

```yaml
services:
  wg-easy:
    image: kaisatoshi/wg-easy-custom:latest
    container_name: wg-easy
    environment:
      - LANG=en
      - WG_HOST=YOUR_SERVER_IP
      - PASSWORD_HASH=$$2a$$12$$YOUR_BCRYPT_HASH
    volumes:
      - etc_wireguard:/etc/wireguard
    ports:
      - "51820:51820/udp"
      - "51821:51821/tcp"
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped

volumes:
  etc_wireguard:
```

**Generate password hash:**
```bash
docker run --rm ghcr.io/wg-easy/wg-easy:14 wgpw 'YOUR_PASSWORD'
```
> Every `$` in the hash must be doubled (`$$`) in the compose file.

**Run:**
```bash
docker compose up -d
```

Panel available at `http://YOUR_SERVER_IP:51821`

### Build from source

```bash
git clone -b v14 https://github.com/KaiSatoshi/wg-easy-custom.git
cd wg-easy-custom
docker build -t wg-easy-custom:latest .
```

### Environment variables

| Variable | Default | Description |
|---|---|---|
| `WG_HOST` | — | ⚠️ Required. Public IP or hostname of your server |
| `PASSWORD_HASH` | — | Bcrypt hash of UI password (double the `$`) |
| `LANG` | `en` | UI language: `en`, `ru`, `de`, `ua`, `tr`, `fr`, `es`, `pl` and more |
| `WG_PORT` | `51820` | WireGuard UDP port |
| `WG_DEFAULT_DNS` | `1.1.1.1` | DNS for clients |
| `WG_DEFAULT_ADDRESS` | `10.8.0.x` | Client IP range |
| `WG_MTU` | `1420` | MTU value |
| `UI_TRAFFIC_STATS` | `false` | Show traffic charts |

### Requirements

- Docker 20+
- Linux with WireGuard kernel module
- No domain required — plain IP over HTTP works fine

### Docker Hub

Pre-built image: [kaisatoshi/wg-easy-custom](https://hub.docker.com/r/kaisatoshi/wg-easy-custom)

### Based on

[wg-easy](https://github.com/wg-easy/wg-easy) by [WeeJeWel](https://github.com/WeeJeWel) — MIT License

---

## Русская версия

Форк [wg-easy](https://github.com/wg-easy/wg-easy) **v14** с добавленным управлением сроком действия клиентов — задавайте время жизни каждого WireGuard конфига прямо из веб-интерфейса.

Работает по **HTTP без домена** (в отличие от v15, которому нужен HTTPS) — подходит для установки на любой VPS по IP адресу.

### Что добавлено по сравнению с оригиналом

- **Срок действия при создании** — поля дни / часы / минуты (оставьте пустым для бессрочного ∞)
- **Бейдж срока** в карточке клиента: ⏳ активный / ⛔ Истёк / ∞ бессрочный
- **Автоотключение** — истёкшие клиенты отключаются автоматически каждую минуту
- **Продление** — кнопка календаря открывает модалку: добавить дни/часы/минуты от текущего момента
- **⛔ Сбросить время** — убирает срок и отключает клиента (аннулирование доступа)
- **∞ Сделать бессрочным** — убирает срок, клиент продолжает работать
- Ручное включение истёкшего клиента → автоматически становится бессрочным
- Интерфейс переведён на все 20 языков оригинала

### Быстрый старт

```yaml
services:
  wg-easy:
    image: kaisatoshi/wg-easy-custom:latest
    container_name: wg-easy
    environment:
      - LANG=ru
      - WG_HOST=IP_ВАШЕГО_СЕРВЕРА
      - PASSWORD_HASH=$$2a$$12$$ВАШ_BCRYPT_ХЭШ
    volumes:
      - etc_wireguard:/etc/wireguard
    ports:
      - "51820:51820/udp"
      - "51821:51821/tcp"
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped

volumes:
  etc_wireguard:
```

**Сгенерировать хэш пароля:**
```bash
docker run --rm ghcr.io/wg-easy/wg-easy:14 wgpw 'ВАШ_ПАРОЛЬ'
```
> Каждый символ `$` в хэше нужно удвоить (`$$`) в файле compose.

**Запуск:**
```bash
docker compose up -d
```

Панель доступна по адресу `http://IP_ВАШЕГО_СЕРВЕРА:51821`

### Docker Hub

Готовый образ: [kaisatoshi/wg-easy-custom](https://hub.docker.com/r/kaisatoshi/wg-easy-custom)
