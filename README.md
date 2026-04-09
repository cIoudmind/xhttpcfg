# xhttpcfg

Настройка XHTTP (node + panel)

------------------------------------------------------------------------

## 📋 Этапы

-   **Шаг 1** --- конфиг в панели\
-   **Шаг 2--4** --- настройка ноды\
-   **Шаг 5--7** --- настройка панели

------------------------------------------------------------------------

## ⚙️ Шаг 1 --- конфиг в панели

![step1](https://github.com/user-attachments/assets/fcae7849-d875-41fe-b014-e6c2d5786508)

👉 Где указана стрелка: - поставить запятую `,` - нажать Enter -
вставить следующий фрагмент в `inbounds`

``` json
{
  "tag": "Sweden_XHTTP",
  "listen": "/dev/shm/xrxh.socket,0666",
  "protocol": "vless",
  "settings": {
    "clients": [],
    "fallbacks": [],
    "decryption": "none"
  },
  "sniffing": {
    "enabled": true,
    "destOverride": [
      "http",
      "tls",
      "quic"
    ]
  },
  "streamSettings": {
    "network": "xhttp",
    "xhttpSettings": {
      "mode": "auto",
      "path": "/xhttppath/",
      "extra": {
        "noSSEHeader": true,
        "xPaddingBytes": "100-1000",
        "scMaxBufferedPosts": 30,
        "scMaxEachPostBytes": 1000000,
        "scStreamUpServerSecs": "20-80"
      }
    }
  }
}
```

------------------------------------------------------------------------

## 🖥️ Шаг 2 --- перезапуск ноды

``` bash
cd /opt/remnanode && docker restart remnanode
```

------------------------------------------------------------------------

## 🖥️ Шаг 3 --- настройка nginx

``` bash
nano /opt/remnanode/nginx.conf
```

![step3](https://github.com/user-attachments/assets/317dd505-c90a-4fe6-9910-aea3ba0c8316)

``` nginx
location /xhttppath/ {
    client_max_body_size 0;
    grpc_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    client_body_timeout 5m;
    grpc_read_timeout 315;
    grpc_send_timeout 5m;
    grpc_pass unix:/dev/shm/xrxh.socket;
}
```

Сохранить: Ctrl + O → Enter → Ctrl + X

------------------------------------------------------------------------

## 🖥️ Шаг 4 --- проверка nginx

``` bash
docker exec remnawave-nginx nginx -t && docker restart remnawave-nginx
```

![step4](https://github.com/user-attachments/assets/c4f92969-1937-4eca-bdb6-0be9610396c6)

------------------------------------------------------------------------

## 🌐 Шаг 5 --- настройка хоста в панели

-   **Inbound**: `xhttp`
-   **Адрес**: IP ноды
-   **Порт**: `443`
-   **SNI**: адрес ноды
-   **Host**: адрес ноды
-   **Path**: `/xhttppath/`
-   **Security Layer**: `tls`
-   **ALPN**: `h2,http/1.1`
-   **Отпечаток**: random

### Доп. настройки (Xray → xHTTP)

``` json
{
  "xmux": {
    "cMaxReuseTimes": 0,
    "maxConcurrency": "16-32",
    "maxConnections": 0,
    "hKeepAlivePeriod": 0,
    "hMaxRequestTimes": "600-900",
    "hMaxReusableSecs": "1800-3000"
  },
  "scMaxEachPostBytes": 1000000,
  "scMinPostsIntervalMs": 30,
  "scStreamUpServerSecs": "20-80"
}
```

``` bash
docker restart remnanode
```

------------------------------------------------------------------------

## 🧩 Шаг 6 --- настройка ноды

![step6](https://github.com/user-attachments/assets/f6adcfc8-7b30-4720-b1eb-4d7c8ef399c8)

------------------------------------------------------------------------

## 🧩 Шаг 7 --- настройка сквадов

![step7](https://github.com/user-attachments/assets/4cde7174-09a0-4a96-bb34-d37888e1c979)

👉 Действия: - default → изменить\
- выбрать ноду\
- оставить только `xhttp`

------------------------------------------------------------------------

## 📎 Источник

https://github.com/legiz-ru/my-remnawave
https://www.youtube.com/watch?v=WiNCQ8PDkVI
