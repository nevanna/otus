# Домашнее задание


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Домашнее задание](#домашнее-задание)
  - [Cоздать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере](#cоздать-новый-проект-в-google-cloud-platform-яндекс-облако-или-на-любых-вм-докере)
  - [Далее создать инстанс виртуальной машины с дефолтными параметрами](#далее-создать-инстанс-виртуальной-машины-с-дефолтными-параметрами)
  - [Добавить свой ssh ключ в metadata ВМ](#добавить-свой-ssh-ключ-в-metadata-вм)
  - [Поставить PostgreSQL](#поставить-postgresql)
    - [Установка](#установка)
    - [Настройка](#настройка)
  - [Зайти вторым ssh (вторая сессия)](#зайти-вторым-ssh-вторая-сессия)
  - [Запустить везде psql из под пользователя postgres](#запустить-везде-psql-из-под-пользователя-postgres)
  - [Выключить auto commit](#выключить-auto-commit)
  - [Сделать в первой сессии новую таблицу и наполнить ее данными](#сделать-в-первой-сессии-новую-таблицу-и-наполнить-ее-данными)
  - [Посмотреть текущий уровень изоляции: show transaction isolation level](#посмотреть-текущий-уровень-изоляции-show-transaction-isolation-level)
  - [Начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции](#начать-новую-транзакцию-в-обоих-сессиях-с-дефолтным-не-меняя-уровнем-изоляции)
  - [В первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');](#в-первой-сессии-добавить-новую-запись-insert-into-personsfirst_name-second_name-valuessergey-sergeev)
  - [Cделать select from persons во второй сессии](#cделать-select-from-persons-во-второй-сессии)
  - [Видите ли вы новую запись и если да то почему?](#видите-ли-вы-новую-запись-и-если-да-то-почему)
  - [Завершить первую транзакцию - commit;](#завершить-первую-транзакцию---commit)
  - [Cделать select from persons во второй сессии](#cделать-select-from-persons-во-второй-сессии-1)
  - [Видите ли вы новую запись и если да то почему?](#видите-ли-вы-новую-запись-и-если-да-то-почему-1)
  - [Завершите транзакцию во второй сессии](#завершите-транзакцию-во-второй-сессии)
  - [Начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;](#начать-новые-но-уже-repeatable-read-транзации---set-transaction-isolation-level-repeatable-read)
  - [В первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');](#в-первой-сессии-добавить-новую-запись-insert-into-personsfirst_name-second_name-valuessveta-svetova)
  - [Cделать select* from persons во второй сессии*](#cделать-select-from-persons-во-второй-сессии-2)
  - [Видите ли вы новую запись и если да то почему?](#видите-ли-вы-новую-запись-и-если-да-то-почему-2)
  - [Завершить первую транзакцию - commit;](#завершить-первую-транзакцию---commit-1)
  - [Cделать select from persons во второй сессии](#cделать-select-from-persons-во-второй-сессии-3)
  - [Видите ли вы новую запись и если да то почему?](#видите-ли-вы-новую-запись-и-если-да-то-почему-3)
  - [Завершить вторую транзакцию](#завершить-вторую-транзакцию)
  - [Сделать select * from persons во второй сессии](#сделать-select--from-persons-во-второй-сессии)
  - [Видите ли вы новую запись и если да то почему?](#видите-ли-вы-новую-запись-и-если-да-то-почему-4)

<!-- /code_chunk_output -->






## Cоздать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере
Создала в яндекс облаке аккаунт и проект

## Далее создать инстанс виртуальной машины с дефолтными параметрами
Создала вм  на Ubuntu 22.04 LTS, прерываемая

```
Идентификатор
    fv42i8honmn2m0le5ohp
Статус
    Running
Имя
    otus
Дата создания
    06.02.2024, в 23:42
Внутренний FQDN
    otus.ru-central1.internal

Зона доступности
    ru-central1-d

Описание
для заданий
Ресурсы

Платформа
    Intel Ice Lake
Гарантированная доля vCPU
    100%
​vCPU
    2
RAM
    4 ГБ
Объём дискового пространства
    18 ГБ
Прерываемая
    Да
```

## Добавить свой ssh ключ в metadata ВМ
подключаюсь с со своей машины по ssh

## Поставить PostgreSQL

### Установка
```bash
# Create the file repository configuration:
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# Import the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# Update the package lists:
sudo apt-get update

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql':
sudo apt-get -y install postgresql

pg_lsclusters
```

### Настройка
* Установка пароля
```bash
#заходим как бы
sudo -i -u postgres psql
#- установка пароля 
/password   
```
* Добавить сетевые правила для подключения к Postgres:

```bash

cd /etc/postgresql/16/main/
sudo nano /etc/postgresql/16/main/postgresql.conf
#listen_addresses = 'localhost'
listen_addresses = '*'

sudo nano /etc/postgresql/16/main/pg_hba.conf
#host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             0.0.0.0/0              scram-sha-256
```
* Рестарт кластера для применения изменений
```bash

sudo pg_ctlcluster 16 main restart
```



так как первая настройка была неудачная, опечатка в пароле, пригодились команды
```bash
# Удаление кластера
sudo pg_dropcluster --stop 16 main
# создание кластера
sudo pg_createcluster 16 main
# запуск кластера
sudo pg_ctlcluster 16 main start
```

была проблема с повторным запуском. была ошибка
```bash
1964 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 16-main start (code=exited, status=1/FAILURE)


 data directory "/var/lib/postgresql/16/main" has invalid permissions2024-02-14 16:07:41.217 UTC [14349] DETAIL:  Permissions should be u=rwx (0700) or u=rwx,g=rx (0750).
```
Полечилось изменением прав директории и установки 700
 ```bash
 #меняем права
 sudo chmod 700 -R /var/lib/postgresql/16/main
 sudo -i -u postgres

 postgres@otus:/$ /usr/lib/postgresql/16/bin/pg_ctl restart -D var/lib/postgresql/16/main
pg_ctl: PID file "var/lib/postgresql/16/main/postmaster.pid" does not exist
Is server running?
trying to start server anyway
waiting for server to start....2024-02-14 16:09:55.020 UTC [14394] LOG:  starting PostgreSQL 16.1 (Ubuntu 16.1-1.pgdg22.04+1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, 64-bit
2024-02-14 16:09:55.020 UTC [14394] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2024-02-14 16:09:55.020 UTC [14394] LOG:  listening on IPv6 address "::", port 5432
2024-02-14 16:09:55.023 UTC [14394] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2024-02-14 16:09:55.100 UTC [14397] LOG:  database system was shut down at 2024-02-07 22:10:46 UTC
2024-02-14 16:09:55.223 UTC [14394] LOG:  database system is ready to accept connections
 done
server started
 ```

* Варианты подключения к бд
1. Из под сессии ssh (c машины) 
 ```bash
 psql -h localhost -U postgres
```
1. Через сеть используем адрес непосредственно вм и порт бд.
Использовала для удаленной работы DBeaver
jdbc:postgresql://{адрес-вируталки}:5432/postgres



## Зайти вторым ssh (вторая сессия)
в два окна терминала подключение
## Запустить везде psql из под пользователя postgres
```bash
psql -h localhost -U postgres
```
## Выключить auto commit
```bash
postgres-# \set AUTOCOMMIT off
postgres-# \echo :AUTOCOMMIT
off
 ```

## Сделать в первой сессии новую таблицу и наполнить ее данными 
```bash
create table persons(id serial, first_name text, second_name text);
```

```bash
insert into persons(first_name, second_name) values('ivan', 'ivanov');
```
```bash
insert into persons(first_name, second_name) values('petr', 'petrov'); commit;
```

## Посмотреть текущий уровень изоляции: show transaction isolation level

```bash
postgres=# SHOW default_transaction_isolation;
 default_transaction_isolation
-------------------------------
 read committed
(1 row)
```

## Начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции

```bash
postgres=# begin ;
BEGIN
```

## В первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');

```bash
postgres=*# insert into persons2(first_name, second_name) values('sergey', 'sergeev');
INSERT 0 1
```

## Cделать select from persons во второй сессии
```bash
postgres=# select * from persons2;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
```

## Видите ли вы новую запись и если да то почему?
нет. В транзакции, работающей на этом уровне, запрос SELECT  видит только те данные, которые были зафиксированы. Мы сделали только вставку без commit


## Завершить первую транзакцию - commit;
```bash
postgres=*# commit;
COMMIT
```


## Cделать select from persons во второй сессии

```
postgres=# select * from persons2;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```

## Видите ли вы новую запись и если да то почему?
Видим, так как мы зафиксировалли данные commit и вызыванный select обратился к зафисированным данным. Хоть мы и получаем аномалию неповторяющегося чтения. В одной транзакции обращение к одной и той же таблице вернули разные значения. Однако для этого уровня изоляции данная аномалия допустима.

## Завершите транзакцию во второй сессии
postgres=*# commit ;
COMMIT



## Начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;

```
postgres=# set transaction isolation level repeatable read;
SET
```

## В первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');
```
postgres=*# insert into persons2(first_name, second_name) values('sveta', 'svetova');
INSERT 0 1
```

## Cделать select* from persons во второй сессии*

```
postgres=*# select * from persons2;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(5 rows)
```

## Видите ли вы новую запись и если да то почему?
нет. Для уровня узоляции repeatable read не характерна аноалия грязного чтения.

## Завершить первую транзакцию - commit;

```
postgres=*# commit ;
COMMIT
```

## Cделать select from persons во второй сессии
```
postgres=*# select * from persons2;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(5 rows)
```


## Видите ли вы новую запись и если да то почему?
нет. Мы еще не завершили транзакцию второй сессии.  Для repeatable read не характерна аномалия неповторяющегося чтения. Неповторяющееся чтение : Мы прочитали строку в т1, потом было изменение строки в т2 и фиксация, мы в т1 пытаемся еще раз прочитать и получаем иное значение от той же строки, те мы никогда не сможе получить значение, которое проитали первый раз. 

## Завершить вторую транзакцию
```
postgres=*# commit;
COMMIT
```


## Сделать select * from persons во второй сессии

```
postgres=# select * from persons2;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  5 | sveta      | svetova
(5 rows)
```

## Видите ли вы новую запись и если да то почему? 
Да видим, на прошлом шаге мы завершили транзакцию. При  select мы начали новую транзакцию с дефолтным уровнем изоляции - Read Committed и прочитали все запросы, которые были зафиксированы.

