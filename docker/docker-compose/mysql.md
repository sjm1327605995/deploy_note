### MYSQL

```yaml
version: '3'
services:
  mysql-prod:
    image: mysql:8.0.28
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - ./mysql/conf/mysql.cnf:/etc/mysql/conf.d/mysql.cnf
      - ./mysql/data:/var/lib/mysql/
      - ./mysql/log:/var/log/mysql/
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: your_password
      MYSQL_ROOT_HOST: "%"

```