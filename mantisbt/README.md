# mantisbt

`MantisBT` is an open source issue tracker that provides a delicate balance between simplicity and power.

## docker-compose.yml

```yaml
version: '3'
services:
  mantisbt:
    image: iwaltgen/mantisbt
    ports:
      - 8989:80
    links:
      - postgres
    volumes:
      - ./data/mantis/config:/var/www/html/config
      - ./data/mantis/custom:/var/www/html/custom
    restart: always

  postgres:
    image: postgres:alpine
    environment:
      - POSTGRES_DB=bugtracker
      - POSTGRES_USER=mantisbt
      - POSTGRES_PASSWORD=mantisbt
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
    restart: always
```

> You can use `mariadb`/`mysql` instead of `postgres`.

## install

```sh
$ open http://localhost:8989/admin/install.php
>>> username: administrator
>>> password: root

==================================================================================
Installation Options
==================================================================================
Type of Database                                        MySQL/MySQLi
Hostname (for Database Server)                          postgres
Username (for Database)                                 mantisbt
Password (for Database)                                 mantisbt
Database name (for Database)                            bugtracker
Admin Username (to create Database if required)         mantisbt
Admin Password (to create Database if required)         mantisbt
Print SQL Queries instead of Writing to the Database    [ ]
Attempt Installation                                    [Install/Upgrade Database]
==================================================================================
```

> generated `./data/mantis/config/config_inc.php`

## email

Append following to `./data/mantis/config/config_inc.php`

```php
$g_phpMailer_method = PHPMAILER_METHOD_SMTP;
$g_administrator_email = 'mantisbt@example.org';
$g_webmaster_email = 'mantisbt@example.org';
$g_return_path_email = 'mantisbt@example.org';
$g_from_email = 'mantisbt@example.org';
$g_smtp_host = 'smtp.example.org';
$g_smtp_port = 587;
$g_smtp_connection_mode = 'tls';
$g_smtp_username = 'mantisbt';
$g_smtp_password = '********';
```
