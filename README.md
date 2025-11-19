# Wordpress Project

by Anton Bogush, Christopher Luft

#### Using ufw(Uncomplicated Firewall)

- port: 443
- port: 80


#### docker compose files:

```compose.yml


services:

  mariadb:
    image: mariadb:11
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - mariadb:/var/lib/mysql
    networks:
      - internal

  wordpress:
    image: wordpress:latest
    restart: always
    environment:
      WORDPRESS_DB_HOST: mariadb
      WORDPRESS_DB_USER: ${MYSQL_USER}
      WORDPRESS_DB_PASSWORD: ${MYSQL_PASSWORD}
      WORDPRESS_DB_NAME: ${MYSQL_DATABASE}
    volumes:
      - wordpress:/var/www/html
    networks:
      - internal
      - proxy
    labels:
      - "traefik.enable=false"

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: always
    environment:
      PMA_HOST: mariadb
      PMA_USER: ${MYSQL_USER}
      PMA_PASSWORD: ${MYSQL_PASSWORD}
    networks:
      - internal
      - proxy
    labels:
      - "traefik.enable=false"

  nginx_proxy_manager:
    image: jc21/nginx-proxy-manager:latest
    restart: always
    ports:
      - "80:80"
      - "81:81"     # Web UI
      - "443:443"
    environment:
      DB_MYSQL_HOST: mariadb
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: ${MYSQL_USER}
      DB_MYSQL_PASSWORD: ${MYSQL_PASSWORD}
      DB_MYSQL_NAME: ${MYSQL_DATABASE}
    volumes:
      - npm_data:/data
      - npm_letsencrypt:/etc/letsencrypt
    networks:
      - proxy
      - internal

volumes:
  mariadb:
  wordpress:
  npm_data:
  npm_letsencrypt:

networks:
  internal:
  proxy:

```
