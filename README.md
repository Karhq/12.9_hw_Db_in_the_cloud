# Домашнее задание к занятию «Базы данных в облаке» - Карпов Роман

---

### Задание 1

#### Создание кластера
1. Перейдите на главную страницу сервиса Managed Service for PostgreSQL.
1. Создайте кластер PostgreSQL со следующими параметрами:
- класс хоста: s2.micro, диск network-ssd любого размера;
- хосты: нужно создать два хоста в двух разных зонах доступности и указать необходимость публичного доступа, то есть публичного IP адреса, для них;
- установите учётную запись для пользователя и базы.

Остальные параметры оставьте по умолчанию либо измените по своему усмотрению.

* Нажмите кнопку «Создать кластер» и дождитесь окончания процесса создания, статус кластера = RUNNING. Кластер создаётся от 5 до 10 минут.
#### Ответ: 
Создаем кластер по рекомендуемым параметрамиз 2-х хостов, PSQL.  
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%B5%D0%BC%20%D0%91%D0%94.png)  

Ожидаем создания кластера, и загрузку хостов.  
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%BA%D0%BB%D0%B0%D1%81%D1%82%D0%B5%D1%80%D0%B0%20.png)  
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%A5%D0%BE%D1%81%D1%82%D1%8B%20%D0%BA%D0%BB%D0%B0%D1%81%D1%82%D0%B5%D1%80%D0%B0.png)  

Кластер и хосты запустились.
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9A%D0%BB%D0%B0%D1%81%D1%82%D0%B5%D1%80%20%D0%B8%20%D1%85%D0%BE%D1%82%D1%81%D1%8B%20%D0%B7%D0%B0%D0%BF%D1%83%D1%81%D1%82%D0%B8%D0%BB%D0%B8%D1%81%D1%8C.png)  

#### Подключение к мастеру и реплике 

* Используйте инструкцию по подключению к кластеру, доступную на вкладке «Обзор»: cкачайте SSL-сертификат и подключитесь к кластеру с помощью утилиты psql, указав hostname всех узлов и атрибут ```target_session_attrs=read-write```.

Подключаемся к мастеру.  
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B0%D0%B5%D0%BC%D1%81%D1%8F%20%D0%BA%20%D0%91%D0%B4%20%D0%BF%D0%BE%20SSH.png)  

* Проверьте, что подключение прошло к master-узлу.
```
select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
```

Проверяем корректность подключения к мастер хосту.
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0%20%D0%BF%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D1%8F%20%D0%BA%20%D0%BC%D0%B0%D1%81%D1%82%D0%B5%D1%80%D1%83.png)  

* Посмотрите количество подключенных реплик:
```
select count(*) from pg_stat_replication;
```

Просматриваем количество подключенных реплик.  
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0%20%D0%BA%D0%BE%D0%BB%D0%B8%D1%87%D0%B5%D1%81%D1%82%D0%B2%D0%B0%20%D0%BF%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%BD%D1%8B%D1%85%20%D1%80%D0%B5%D0%BF%D0%BB%D0%B8%D0%BA.png)  

### Проверьте работоспособность репликации в кластере

* Создайте таблицу и вставьте одну-две строки.
```
CREATE TABLE test_table(text varchar);
```
```
insert into test_table values('Строка 1');
```

На мастер хосте, создаем таблицу и добавлем в нее 3 строки. 
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%B5%D0%BC%20%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%83%20%D0%B8%20%D0%B4%D0%BE%D0%B1%D0%B0%D0%B2%D0%BB%D1%8F%D0%B5%D0%BC%20%D0%BA%20%D0%BD%D0%B5%D0%B9%20%D1%81%D1%82%D1%80%D0%BE%D1%87%D0%BA%D0%B8%20.png)  

* Выйдите из psql командой ```\q```.

* Теперь подключитесь к узлу-реплике. Для этого из команды подключения удалите атрибут ```target_session_attrs```  и в параметре атрибут ```host``` передайте только имя хоста-реплики. Роли хостов можно посмотреть на соответствующей вкладке UI консоли.

Меняем команду подключения на следующую: 
```
psql "host=rc1c-uwm4va691gxojtdo.mdb.yandexcloud.net \
      port=6432 \
      sslmode=verify-full \
      dbname=db_netology \
      user=karhu"
```
* Проверьте, что подключение прошло к узлу-реплике.
```
select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
```

и подключаемся к реплике хосту.  
Так же проверям, что мы подключились действительно к реплике.

![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D0%BE%D0%B4%D0%BA%D0%BB%D1%8E%D1%87%D0%B0%D0%B5%D0%BC%D1%81%D1%8F%20%D0%BA%20%D1%80%D0%B5%D0%BF%D0%BB%D0%B8%D0%BA%D0%B5%20.png)  

* Проверьте состояние репликации
```
select status from pg_stat_wal_receiver;
```
Проверка состояния репликации. 
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D1%8F%D0%B5%D0%BC%20%D1%81%D1%82%D0%B0%D1%82%D1%83%D1%81%20%D1%80%D0%B5%D0%BF%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D0%B8.png)  
* Для проверки, что механизм репликации данных работает между зонами доступности облака, выполните запрос к таблице, созданной на предыдущем шаге:
```
select * from test_table;
```

Проверяем доступность созданной ранее на мастер хотсе таблицы, с реплики. 
![Скрин](https://github.com/Karhq/12.9_hw_Db_in_the_cloud/blob/main/%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D1%8F%D0%B5%D0%BC%20%D0%B4%D0%BE%D1%81%D1%82%D1%83%D0%BF%D0%BD%D0%BE%D1%81%D1%82%D1%8C%20%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B%20%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%BD%D0%BE%D0%B9%20%D0%BD%D0%B0%20%D0%BC%D0%B0%D1%81%D1%82%D0%B5%D1%80%D0%B5%20%D1%81%20%D1%80%D0%B5%D0%BF%D0%BB%D0%B8%D0%BA%D0%B8.png)  
  
