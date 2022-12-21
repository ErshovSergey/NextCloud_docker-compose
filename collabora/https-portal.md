version: '3'

services:
  https-portal:
    image: steveltn/https-portal:1
    container_name: "${COMPOSE_PROJECT_NAME}_container"
    ports:
      - '${HTTP_IP}:443:443'
      - '${HTTP_IP}:80:80'
      - '${HTTP_IP2}:443:443'
      - '${HTTP_IP2}:80:80'

    environment:
      WEBSOCKET: 'true'
      PROXY_CONNECT_TIMEOUT: 300
      KEEPALIVE_TIMEOUT: 5
      PROXY_SEND_TIMEOUT: 300
      PROXY_READ_TIMEOUT: 300
      CLIENT_MAX_BODY_SIZE: 2G
      CUSTOM_NGINX_SERVER_CONFIG_BLOCK: |
        proxy_buffering off;
        proxy_http_version 1.1;
        client_max_body_size 100M;
        proxy_set_header Host $$http_host;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Upgrade $$http_upgrade;
#      STAGE: local


# "->" - проксирование
# "=>" - редирект
# #local #staging #production
#      DOMAINS: 'example.com                                                     ->      ${HOST_GUACAMOLE} #local,
      DOMAINS: 'colla.nextcloud.c.ru           ->   https://10.10.b.a:9980 #production,
                nextcloud.c.ru                    ->     http://10.10.b.a:8080 #production'

    volumes:
      - "/etc/localtime:/etc/localtime:ro"
      - "${DATA_PATH}/https-portal-data:/var/lib/https-portal"
      - "${DATA_PATH}/log:/var/log/nginx/"
      - "${DATA_PATH}/logrotate:/var/lib/logrotate/"

    restart: always
