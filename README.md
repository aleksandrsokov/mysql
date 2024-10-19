# SQL
при помощи Vagrant разворачиваются 2 ВМ,  
а так же производитьтся установка и первоначальная настройка percona

создаем базу CREATE DATABASE bet;  
загружаем дамп mysql -uroot -p -D bet < /tmp/bet.dmp

создаем пользователя для репликации:  
CREATE USER 'repl'@'%' IDENTIFIED BY '!OtusLinux2018';  
SELECT user,host FROM mysql.user where user='repl';  
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY '!OtusLinux2018';  


создаем дамп 
mysqldump --all-databases --triggers --routines --master-data --ignore-table=bet.events_on_demand --ignore-table=bet.v_same_event -uroot -p > master.sql

копируем дамп на реплику
scp master.sql vagrant@192.168.50.20:/tmp/

вывод после импорта  
mysql> SHOW TABLES;
+---------------+
| Tables_in_bet |
+---------------+
| bookmaker     |
| competition   |
| market        |
| odds          |
| outcome       |
+---------------+

подключаем реплику  
CHANGE MASTER TO MASTER_HOST = "192.168.50.10", MASTER_PORT = 3306, MASTER_USER = "repl", MASTER_PASSWORD = "!OtusLinux2018", MASTER_AUTO_POSITION = 1;  


проверка репликации:  
![alt text](<Screenshot from 2024-10-19 15-38-48.png>)  
![alt text](<Screenshot from 2024-10-19 15-39-31.png>)  

скрин binlog файла:  
![alt text](<Screenshot from 2024-10-19 15-36-34.png>)

