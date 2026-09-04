packet name -  ``mysql-server``
service name - ``mysqld`` ``mysqld.service``

https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_using_database_servers/assembly_using-mysql_configuring-and-using-database-servers#installing-mysql_assembly_using-mysql

Create Database 
```sql
CREATE DATABASE name;
```

Create User
```
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

GRANT ACCESS 
https://dev.mysql.com/doc/refman/8.4/en/grant.html
```
GRANT ALL PRIVILEGES ON databasename.* TO 'appuser'@'localhost'; 
```

```
FLUSH PRIVILEGES;
```


network for database 
``/etc/my.cnf.d/mariadb-server.cnf``
normally port 3306