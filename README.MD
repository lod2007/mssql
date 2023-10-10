
# English language readme.md
[English](./readme/README_en.MD)

# Описание

Данный проект позволяет развернуть в Docker MS SQL Server 2017.
За основу взят image: mcr.microsoft.com/mssql/server:2017-latest
Подробности [по ссылке](https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-bash)

![WordPress Plugin Version](https://img.shields.io/badge/mssql-2017-green)

Для получения копии этого проекта необходимо клонировать проект к себе на машину командой:
```
$git clone https://github.com/lod2007/mssql.git
```


В корне проекта создать файл с переменными окружения .env:

![.env](./img/1.png)

добавьте в файл .env следующие строки:
```
MSSQL_SA_PASSWORD=YourStrong@Passw0rd
```
где:
 - MSSQL_SA_PASSWORD - пароль SA (Specify your own strong password that is at least eight characters and meets the SQL Server password requirements. Required setting for the SQL Server image)

#### Volumes

 - ./mssql_db/data - каталог с БД
 - ./mssql_db/log - каталог логов
 - ./mssql_db/secrets - каталог секретов
 - ./mssql_db/back_up - каталог для бэкапов. Через этот каталог можно заносить БД в контейнер.

### Запуск контейнера
```
docker-compose up -d
```

### Провалиться в контейнер
```
docker exec -u root -it mssql /bin/bash
```

### Установка поумолчанию кирилицы для таблиц 
Проваливаемся в контейнер
```
docker exec -u root -it mssql /bin/bash
```
Внутри контейнера пробуем выполнить: 
```
useradd mssql
chmod -R 777 /var/opt/mssql
echo "Cyrillic_General_CI_AS" | /opt/mssql/bin/mssql-conf set-collation
```

## Change the system administrator password
The SA account is a system administrator on the SQL Server instance that gets created during setup. After you create your SQL Server container, the MSSQL_SA_PASSWORD environment variable you specified is discoverable by running echo $MSSQL_SA_PASSWORD in the container. For security purposes, change your SA password.

Choose a strong password to use for the SA user.

Use docker exec to run sqlcmd to change the password using Transact-SQL. In the following example, the old and new passwords are read from user input.

```
sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
-S localhost -U SA \
 -P "$(read -sp "Enter current SA password: "; echo "${REPLY}")" \
 -Q "ALTER LOGIN SA WITH PASSWORD=\"$(read -sp "Enter new SA password: "; echo "${REPLY}")\""
 ```