<h1>
    <img align="center" width="40px" src="./docker-mark-blue.svg" alt="Docker logo">
    <span>Criando um Docker Compose do Zabbix Server</span>
</h1>

Repositório desenvolvido para fins educativos.

## Objetivo

Criar um Docker Compose para executar o Zabbix Server para realizar as seguintes tarefas:

- Mapear um volume para o banco de dados

- Expor a porta da interface web do Zabbix

- Expor a porta do Grafana

### A configuração devera ter os seguintes contêineres:

- mysql

- zabbix-server-mysql

- zabbix-web-nginx-mysql

- grafana

## Exemplo de Docker Compose

```
version: '3.7'

services:
  mysql-server:
    image: mysql:8.0
    container_name: mysql-server
    environment:
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
    networks:
      - zabbix-net
    volumes:
      - mysql-data:/var/lib/mysql
    restart: unless-stopped
    command: --character-set-server=utf8 --collation-server=utf8_bin --default-authentication-plugin=mysql_native_password

  zabbix-server-mysql:
    image: zabbix/zabbix-server-mysql:alpine-6.4-latest
    container_name: zabbix-server-mysql
    environment:
      DB_SERVER_HOST: "mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
    networks:
      - zabbix-net
    ports:
      - "10051:10051"

  zabbix-web-nginx-mysql:
    image: zabbix/zabbix-web-nginx-mysql:alpine-6.4-latest
    container_name: zabbix-web-nginx-mysql
    environment:
      ZBX_SERVER_HOST: "zabbix-server-mysql"
      DB_SERVER_HOST: "mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix_pwd"
      MYSQL_ROOT_PASSWORD: "root_pwd"
    networks:
      - zabbix-net
    ports:
      - "80:8080"
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    networks:
      - zabbix-net
    ports:
      - "3000:3000"
    restart: always

networks:
  zabbix-net:
    driver: bridge

volumes:
  mysql-data:
```

## Instruções do Docker Compose

### Execute o contêiner

No diretório onde está o arquivo docker-compose.yml, execute:

```
sudo docker-compose up -d
```
