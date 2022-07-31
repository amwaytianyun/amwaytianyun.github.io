```
version: '2.0'
services:
  db:
    image: mariadb
    container_name: seafile-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=*******
      - MYSQL_LOG_CONSOLE=true
    volumes:
      - ./data/mariadb:/var/lib/mysql
    networks:
      - seafile-net
  memcached:
    image: memcached
    container_name: seafile-memcached
    entrypoint: memcached -m 256
    networks:
      - seafile-net

  seafile:
    image: seafileltd/seafile-mc
    container_name: seafile
    ports:
      - "7080:80"
    volumes:
      - ./data/app:/shared
    environment:
      - DB_HOST=db
      - DB_ROOT_PASSWD=********
      - TIME_ZONE=Etc/UTC
      - SEAFILE_ADMIN_EMAIL=****
      - SEAFILE_ADMIN_PASSWORD=****
      - SEAFILE_SERVER_LETSENCRYPT=false
      - SEAFILE_SERVER_HOSTNAME=****
