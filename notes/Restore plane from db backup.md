### Prerequisites:

1. **MySQL** database
2. **MySQL Credentions**
3. The **SQL dump file** (e.g., `backup.sql`) you want to restore from.


### Step-by-Step Guide:
0. **Install DB**
Need to follow **first step** in **VMware install guide** to prepare all project and create **DB**

2. **Create a new database** (if you want to restore the dump into a new database):
```shell
mysql -u root -p -e "CREATE DATABASE new_database_name;"
```
Replace `root` with your MySQL username if it's different, and `new_database_name` with the desired name of the new database.

2. **Restore the database** from the SQL dump file:
```shell
mysql -u root -p new_database_name < /path/to/backup.sql
```
Replace `root` with your MySQL username, `new_database_name` with the name of the database you created (or an existing one), and `/path/to/backup.sql` with the path to your SQL dump file.

3. **Start docker** project in server
```shell
docker compose up -d --build
```


[[work]]
[[vmware]]