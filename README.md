# xhttpcfg
шаг 1 конфиг в панели
шаг 2-4 нода
шаг 5-7 панель

шаг 1
<img width="557" height="187" alt="image" src="https://github.com/user-attachments/assets/fcae7849-d875-41fe-b014-e6c2d5786508" />

Где стрелка, нужна запятая потом энтер и вставь фрагмент
'''{
      "tag": "Sweden_XHTTP", ## Вместо "Sweden" ставим страну сервера или убираем
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
    }'''

шаг 2
'''cd /opt/remnanode && docker restart remnanode'''

шаг 3
'''nano /opt/remnanode/nginx.conf'''

<img width="630" height="284" alt="image" src="https://github.com/user-attachments/assets/317dd505-c90a-4fe6-9910-aea3ba0c8316" />

и вставляешь этот код, где помечено красным там энтер 
'''location /xhttppath/ {
            client_max_body_size 0;
            grpc_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_body_timeout 5m;
            grpc_read_timeout 315;
            grpc_send_timeout 5m;
            grpc_pass unix:/dev/shm/xrxh.socket;
        }'''
control+o, enter, control+x

шаг 4
'''docker exec remnawave-nginx nginx -t && docker restart remnawave-nginx'''
должно показать 


<img width="567" height="68" alt="image" src="https://github.com/user-attachments/assets/c4f92969-1937-4eca-bdb6-0be9610396c6" />

шаг 5
хосты -> новый хост -> в инбаундах берешь xhttp
адрес = адрес ноды
порт 443 (может слететь, так что перепроверь)
в расширенных
SNI = адрес ноды
хост = адрес ноды
путь = /xhttppath/
security layer = tls
ALPN = h2,http/1.1
отпечаток = рандом
настройки xray -> xHTTP
'''{
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
}'''

и сохраняем
'''docker restart remnanode'''

шаг 6(раздел ноды)
ждем пока стартанет нода и в инбаунде ставим основной конфиг+xhttp


<img width="426" height="190" alt="image" src="https://github.com/user-attachments/assets/f6adcfc8-7b30-4720-b1eb-4d7c8ef399c8" />

шаг 7(сквады)
<img width="490" height="670" alt="image" src="https://github.com/user-attachments/assets/4cde7174-09a0-4a96-bb34-d37888e1c979" />


дефолт -> изменить -> нода и ставим только xhttp


пользовался гайдом от легиза https://github.com/legiz-ru/my-remnawave
