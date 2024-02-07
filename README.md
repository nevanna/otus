#Домашнее задание




##Cоздать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере
Создала в яндекс обалке аккаунт и проект

##Далее создать инстанс виртуальной машины с дефолтными параметрами
Создала вм  на Ubuntu 22.04 LTS, прерываемая

##Добавить свой ssh ключ в metadata ВМ
подключаюсь с со своей машины по ssh

##Поставить PostgreSQL

###Установка
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

###Настройка
* Установка пароля
```
#заходим как бы
sudo -i -u postgres psql
#- установка пароля 
/password   
```
* Добавить сетевые правила для подключения к Postgres:

```

cd /etc/postgresql/16/main/
sudo nano /etc/postgresql/16/main/postgresql.conf
#listen_addresses = 'localhost'
listen_addresses = '*'

sudo nano /etc/postgresql/16/main/pg_hba.conf
#host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             0.0.0.0/0              scram-sha-256
```
* Рестарт кластера для применения изменений
```

sudo pg_ctlcluster 16 main restart
```



так как первая настройка была неудачная, опечатка в пароле, пригодились команды
```
#Удаление кластера
sudo pg_dropcluster --stop 16 main
#создание кластера
sudo pg_createcluster 16 main
#запуск кластера
sudo pg_ctlcluster 16 main start
```

* Варианты подключения к бд
1. Из под сессии 
 ```
 psql -h localhost -U postgres
```
1. Через сеть используем адрес непосредственно вм и порт бд.
Использовала для удаленной работы DBeaver
jdbc:postgresql://{адрес-вируталки}:5432/postgres



# Зайти вторым ssh (вторая сессия)
в два окна терминала подключение
# Запустить везде psql из под пользователя postgres
# Выключить auto commit
```
 \set AUTOCOMMIT off

 \echo :AUTOCOMMIT
 ```

#Сделать в первой сессии новую таблицу и наполнить ее данными 
```
create table persons(id serial, first_name text, second_name text);

insert into persons(first_name, second_name) values('ivan', 'ivanov');

insert into persons(first_name, second_name) values('petr', 'petrov'); commit;
```