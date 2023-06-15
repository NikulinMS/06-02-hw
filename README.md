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

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

---
## Дополнительные задания (со звездочкой*)


### Задание 5

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
