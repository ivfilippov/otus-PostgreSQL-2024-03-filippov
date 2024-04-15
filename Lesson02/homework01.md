# SQL и реляционные СУБД. Введение в PostgreSQL  



**Установка postgres**

```
sudo apt-get update&&sudo apt-get upgrade
sudo apt install postgresql
```
___

**Подключаемся двумя сессиями и отключаем автокоммит**
```
adm-ivfilippov@ivfhome:~$ psql -U postgres

psql (14.11 (Ubuntu 14.11-0ubuntu0.22.04.1))
Type "help" for help.

postgres=# \set AUTOCOMMIT off
```
___


Далее обозначения:  
*ssh01* - первая сессия,  
*ssh02* - вторая сессия
___

**Создаем таблицу**   

*ssh1*  
```
postgres=# create table persons(id serial, first_name text, second_name text);
insert into persons(first_name, second_name) values('ivan', 'ivanov');
insert into persons(first_name, second_name) values('petr', 'petrov');

CREATE TABLE
INSERT 0 1
INSERT 0 1

postgres=# commit;

COMMIT
```
**Проверяем текущий уровень изоляции**

*ssh1*
```
postgres=# show transaction isolation level;

 transaction_isolation  
-----------------------
 read committed  
(1 row)
```

**Добавляем новую запись**

*ssh1*
```
postgres=#  insert into persons(first_name, second_name) values('sergey', 'sergeev');

INSERT 0 1
```

**Проверяем во втором терминале**  

*ssh2*
```
postgres=#select * from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)
```

Мы не видим новую запись во второй сессии, потому что для этого уровня изоляции параллельно исполняющиеся транзакции видят только завершенные изменения из других транзакций. 
Таким образом, данный уровень обеспечивает защиту от *грязного чтения*.

*ssh01*
```
postgres=# commit;
COMMIT
```

*ssh2*
```
postgres=#  select * from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
Мы видим новую запись, потому что изменения в транзакции были завершены коммитом в первой сессии.
На этом уровне изоляции мы наблюдаем *феномен чтения фантомов*, когда мы видим добавленные и закоммиченные записи других транзакций

*ssh2*
```
postgres=# commit;
COMMIT
```
   
___  


**Меняем уровень изоляции**

*ssh01*
```
postgres=#  set transaction isolation level repeatable read;

SET

postgres=# show transaction isolation level;

 transaction_isolation
-----------------------
 repeatable read
(1 row)
```

**Добавляем строку**

*ssh01*
```
postgres=# insert into persons(first_name, second_name) values('sveta', 'svetova');

INSERT 0 1
```
**Смотрим что видно во второй сесии**

*ssh2*
```
postgres=#  select * from persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
Мы не видим новую запись, потому что не был выполнен commit в первой сессии, *феномен грязного чтения* отсутствует

**Подтверждаем транзакцию **

*ssh01*
```
postgres=# commit;

COMMIT
```
**Проверяем во второй сессии**

*ssh2*
```
postgres=#  select * from persons;

 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
Мы снова не видим добавленные строки, так как транзакции во второй сессии не завершены коммитом, *феномен чтения фантомов* отсутствует, хотя в классическом представлении мы должны наблюдать этот эффект.

**Подтврждаем транзакцию**

*ssh02* 
```
postgres=# commit;

COMMIT
```
**Проверяем таблицу снова**

*ssh2*  
```
postgres=#  select * from persons;

 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | sveta      | svetova
(4 rows)
```
После завершения транзакций во всех сессиях все стоки видимы


