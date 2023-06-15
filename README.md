# Домашнее задание к занятию "`SQL`" - `Никулин Михаил Сергеевич`



---

### Задание 1

[centos@node01 docker]$ sudo docker pull postgres:12

docker-compose.yaml

```

version: '3'
services:
 db:
   container_name: pg12
   image: postgres:12
   environment:
     POSTGRES_USER: nikulinm
     POSTGRES_PASSWORD: 123456
     POSTGRES_DB: netology_db
   ports:
     - "5432:5432"
   volumes:
     - database_volume:/home/database/
     - backup_volume:/home/backup/

volumes:
 database_volume:
 backup_volume:

```
Поднимаем docker-compose и запускаем bash внутри контейнера pg12:

```
[root@node01 docker]# docker-compose up -d
Creating network "docker_default" with the default driver
Creating volume "docker_database_volume" with default driver
Creating volume "docker_backup_volume" with default driver
Creating pg12 ... done
[root@node01 docker]# docker exec -it pg12 bash
root@1e4fa475e538:/#
```


---

### Задание 2

- создадим БД test_db и выполним подключение к созданной базе:
```
root@1e4fa475e538:/# createdb test_db -U nikulinm
root@1e4fa475e538:/# psql -d test_db -U nikulinm
psql (12.15 (Debian 12.15-1.pgdg120+1))
Type "help" for help.
```
- создадим пользователя test-admin-user:
```
test_db=# CREATE USER test_admin_user;
CREATE ROLE
```
- в БД test_db создадим таблицы orders и clients:
```
test_db=# CREATE TABLE orders
(
   id SERIAL PRIMARY KEY,
   наименование TEXT,
   цена INTEGER
);
CREATE TABLE
test_db=# CREATE TABLE clients
(
    id SERIAL PRIMARY KEY,
    фамилия TEXT,
    "страна проживания" TEXT,
    заказ INTEGER,
    FOREIGN KEY (заказ) REFERENCES orders(id)
);
CREATE TABLE
test_db=# CREATE INDEX country_index ON clients ("страна проживания");
CREATE INDEX
```
- предоставим привилегии на все операции пользователю test-admin-user на таблицы БД test_db:
```
test_db=# CREATE INDEX country_index ON clients ("страна проживания");
CREATE INDEX
test_db=# GRANT ALL ON TABLE orders TO test_admin_user;
GRANT
test_db=# GRANT ALL ON TABLE clients TO test_admin_user;
GRANT
```
- создадим пользователя test-simple-user:
```
test_db=# CREATE USER test_simple_user;
CREATE ROLE
```
- предоставим пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db:
```
test_db=# GRANT SELECT,INSERT,UPDATE,DELETE ON TABLE orders TO test_simple_user;
GRANT
test_db=# GRANT SELECT,INSERT,UPDATE,DELETE ON TABLE clients TO test_simple_user;
GRANT
```
- итоговый список БД после выполнения пунктов выше:
![task_2_1.png](img%2Ftask_2_1.png)
- описание таблиц (describe):
![task_2_2.png](img%2Ftask_2_2.png)
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db:
```
SELECT grantee, table_catalog, table_name, privilege_type FROM information_schema.table_privileges WHERE table_name IN ('orders','clients');
```
- список пользователей с правами над таблицами test_db:
![task_2_3.png](img%2Ftask_2_3.png)

---

### Задание 3

- наполним таблицы требуемыми тестовыми данными:
```
test_db=# INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
test_db=# INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```
- SQL-запросы для вычисления количества записей в таблицах:
```
SELECT COUNT (*) FROM orders;
SELECT COUNT (*) FROM clients;
```
![task_3_1.png](img%2Ftask_3_1.png)

---

### Задание 4

- свяжем записи из таблиц следующими запросами:
```
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Книга') WHERE фамилия='Иванов Иван Иванович';
UPDATE 1
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Монитор') WHERE фамилия='Петров Петр Петрович';
UPDATE 1
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Гитара') WHERE фамилия='Иоганн Себастьян Бах';
UPDATE 1
```
- с помощью запроса ```SELECT * FROM clients WHERE заказ IS NOT NULL;``` выведем пользователей, которые совершили заказ:
![task_4_1.png](img%2Ftask_4_1.png)

---


### Задание 5

![task_5_1.png](img%2Ftask_5_1.png)
Чтение данных происходит с использованием метода Seq Scan - последовательного чтения данных. Значение 0.00 — ожидаемые затраты времени на получение первой строки. Второе — 18.10 — ожидаемые затраты времени на получение всех строк. rows - ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца. width - ожидаемый средний размер строк, выводимых этим узлом плана (в байтах). Каждая запись сравнивается с условием "заказ" IS NOT NULL. Если условие выполняется, запись вводится в результат. Иначе — отбрасывается. 

Теперь, как лбрабатывается запрос в реальных условиях, используем директиву ANALYZE:
![task_5_2.png](img%2Ftask_5_2.png)

---

### Задание 6

- Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов:
```
root@1e4fa475e538:/# pg_dump -U nikulinm test_db > /home/backup/test_db.backup
```
- Остановим контейнер с PostgreSQL (pg12):
```
[root@node01 docker]# docker stop pg12
```
![task_6_1.png](img%2Ftask_6_1.png)
- Поднимем новый пустой контейнер с PostgreSQL:
```
[root@node01 docker]# docker run --name pg12_test -e POSTGRES_PASSWORD=1234567 -d postgres:12
```
- Восстановите БД test_db в новом контейнере. Для этого скопируем файл дампа из контейнера pg12 в контейнер pg12_new:
```
[root@node01 docker]# docker cp pg12:/home/backup/test_db.backup backup/ && docker cp backup/test_db.backup pg12_tes
t:/home/
```
- и восстановим базу:
```
root@53d1ec57b693:/# createdb test_db -U postgres
root@53d1ec57b693:/# psql -U postgres -d test_db -f /home/test_db.backup
```
![task_6_2.png](img%2Ftask_6_2.png)
![task_6_3.png](img%2Ftask_6_3.png)