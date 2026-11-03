# AdGuard Home на VPS для AmneziaWG

Блокировка рекламы и трекеров через DNS для клиентов AmneziaWG.

## 1. Клонировать и запустить

```bash
git clone https://github.com/Macsin921/vps-adguard-public.git
cd vps-adguard-public
cp .env.example .env
mkdir -p adguard/work adguard/conf
docker compose up -d
```

## 2. Первичная настройка панели

Панель не открыта в интернет. Подключись по SSH-туннелю со своего компьютера:

```bash
ssh -L 3000:127.0.0.1:3000 root@65.21.25.180
```

Затем в браузере открой:

```text
http://127.0.0.1:3000
```

При первичной настройке:

- Listen interface: `0.0.0.0:3000` внутри контейнера — не менять.
- DNS listen interface: `0.0.0.0:53` внутри контейнера — не менять.
- Создай логин и пароль администратора.

## 3. DNS для AmneziaWG

В AmneziaVPN:

1. Открой настройки подключения.
2. В поле DNS укажи:

```text
10.9.9.1
```

Если этот адрес не сработает, проверь IP AdGuard Home:

```bash
docker inspect adguardhome --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
```

И подставь полученный IP в DNS клиента.

## 4. Проверка

На устройстве с включённым VPN:

```bash
nslookup doubleclick.net 10.9.9.1
```

Ожидаемый результат: адрес `0.0.0.0` либо блокировка запроса.

## 5. Управление

```bash
docker compose ps
docker compose logs -f adguardhome
docker compose restart
docker compose down
```

## Безопасность

- Порты `53` и `3000` проброшены только на `127.0.0.1`, поэтому снаружи сервера они недоступны.
- Панель открывай только через SSH-туннель.
- Не публикуй `.env`, данные AdGuard или доступ к панели.
