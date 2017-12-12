## Overview

PostgreSQL is open-source Object-Relational Database Management System. In this tutorial, we will demonstrate how to install and use PostgreSQL, together with Python interface. 

## Installation

For installing Postgresql, released binary packages or package managers can be used. 

* For UNIX, apt-get can be used for installing the Postgresql.

```bash
sudo apt-get install postgresql
```

* For MAC OS, [Brew](https://brew.sh/) package manager can be used as follows:

```bash
brew install postgresql
```

* For Windows, the binary package in [Postgresql website](https://www.postgresql.org/download/windows/).

---

After installation, the Postgresql service should be started. In Windows, it is automatically registered for startup applications. 

* For UNIX, the service can be started with the command:

```bash
service postgresql start
```

* For MAC OS, the service can be started with the command:

```bash
pg_ctl -D /usr/local/var/postgres start
```

***

The default installation of Postgresql creates a user named with **postgres**. For accessing the command line interface of Postgresql, firstly the current user should be changed to **postgres** and **psql** command should be called. 

```bash
sudo su - postgres
psql
```

If you see **postgres=#** on the command line, you successfully logged in to Postgresql interface! 


## Create - Drop - Connect Database 

The database operation can be done via SQL commands as follows:

```sql
postgres=# CREATE DATABASE <database_name>;
postgres=# DROP DATABASE <database_name>;
postgres=# DROP DATABASE IF EXISTS <database_name>;
```

The latter command for droping a database does not throw an error if a database is not found given name. The databases can be listed by **\l** command. 

You can connect a database with command: `\c <database_name>`

**Now, create a database with name tutorialdb and connect to it!** 

## Tables and Data 

Now, lets create a table in our tutorialdb database, called tutorialstudents. We make this table have 3 fields as name, id and age. Id and age will be integer where Id cannot be null, and name will be variable length char array, in other words, string. Name is not nullable too. All data types supported by Postgresql can be accessed via this [link](https://www.postgresql.org/docs/9.2/static/datatype.html).

Use the following command: 
```sql
tutorialdb=# CREATE TABLE tutorialstudents (
	name varchar(30) NOT NULL,
    id integer NOT NULL,
    age integer
);
```

And insert 2 samples values to this table: 
```sql 
tutorialdb=# INSERT INTO tutorialstudents values('Student 1', 1001, 20);
tutorialdb=# INSERT INTO tutorialstudents values('Student 2', 1002, 24);
```

Here is a sample query, finding the students name with age greater than 22. 
```sql
tutorialdb=# SELECT name FROM tutorialstudents WHERE age > 22;
```

These commands can be used via Postgresql Programming Interfaces to handle the database operation of programs, especially large scaled ones. The set of all SQL commands can be find via `\h` or `\help` command. Another example of SQL commands will be given during programming part of the tutorial. 



## PostgreSQL Programming Interfaces
PostgreSQL is generally used as a backend of various applications. Interaction with PostgreSQL is done via database drivers. Most programming languages like Python, Java, PhP, C/C++ are supported.

   ### PostgreSQL Python
Python has various drivers such as *psycopg2, bpgsql, pg8000, PyGreSQL. psycopg2* is one of the most popular ones.

#### Psycopg2
Psycopg2 is available on PyPI in the form of wheel packages. The following command can be used to install:

	$ pip install psycopg2

The psycopg2 matches Python objects to the PostgreSQL data types e.g., list to the array, tuples to records, and dictionary to hstore.

##### GETTING STARTED

You can start by downloading the code [psycopg2 Tutorial](https://github.com/ogzincdlp/psycopg2Tutorial).

In order to start interacting with the database, we will create and connect to a database. You can create a new database with the following command:

```sql 
CREATE DATABASE suppliers;
```


We will connect to that database using the connect() function of psycopg2 module.
```python
conn = psycopg2.connect(host="localhost",database="suppliers", user="postgres", password="postgres")
```

	
You may need to change user and password if you set them to be anything other than default parameters. You can refer to the following list of parameters for the connect() function:

- database: the name of the database that you want to connect.
- user: the username used to authenticate.
- password: password used to authenticate.
- host: database server address e.g., localhost or an IP address
- port: the port number that defaults to 5432 if it is not provided.

After connecting to the database, we will create a cursor and use it to execute SQL commands.

To make this setup process more convenient, we can create a configuration file. The parameters are described in database.ini file. The database name is currently missing. Set database to be *suppliers*. config() function parses this file and configures connection to this database. 

connect() function starts a connection using this helper method. If an error occurs connect() function raises DatabaseError. You can try changing database.ini file. connect() function should raise an appropriate error.


Now that, we connected to the database we can start manipulating it. We will create tables, populate them, query some data, update, and delete rows. In the following set of sections the general flow of the code is the following:
* Connect to the database (as we did before)
* Do Something (Create tables, Query data, Insert Data…)
* Close the connection

##### CREATE

In create file we have a create_tables() function that we will use to create a couple of tables. Most of the tables are already there but we are missing vendors table. Complete that command on line 8 so that it looks like the command that creates parts table. The names of the two columns should be vendors_id and vendors_name.

Now if you should be able to query those tables (`SELECT * FROM vendors;`). You will not get any results since the tables are empty but you should not get an error.

##### INSERT

Now we can populate those tables. In insert file, we have two functions to insert one vendor or a list of vendors. The structure of the functions are pretty much the same as the create_tables function. You can now call those functions and pass some vendor names to those functions. You can try pasting this in the if block at the bottom of the page (delete the keyword `pass` afterwards):
```python
# insert one vendor
insert_vendor("3M Co.")

# insert multiple vendors
insert_vendor_list([
    ('AKM Semiconductor Inc.',),
    ('Asahi Glass Co Ltd.',),
    ('Daikin Industries Ltd.',),
    ('Dynacast International Inc.',),
    ('Foster Electric Co. Ltd.',),
    ('Murata Manufacturing Co. Ltd.',)
])
```


##### QUERY

Let’s see if we can query the data we just inserted. In query file, we will execute a SELECT statement. Fill out the input to the execute function (on line 12). An example statement might be:

```sql
SELECT vendor_id, vendor_name FROM vendors ORDER BY vendor_name
```

You should be able to see the vendors you inserted in the previous step.

##### UPDATE

Let’s try using UPDATE command. update_vendor() function in update file, updates the vendor name for a given id. Try changing names of the vendors you entered.  You can call that function and pass vendor_id and vendor_name to it in the if block towards the end of the file. For example:
```python
if __name__ == '__main__':
   	# update vendor name
    update_vendor(1, "3M Corp")
```

##### DELETE

Lastly, let’s try deleting one entry. delete_vendor() function in delete file is almost complete. Complete the command on line 16. Input to the execute function should look like this:
```python
"DELETE FROM vendors WHERE vendor_id = %s", (vendor_id,)
```

Now, you can call that function to delete some entries.
```python
if __name__ == '__main__':
	# delete vendor 5
    deleted_rows = delete_vendor(5)
    print('The number of deleted rows: ', deleted_rows)
```

   
   
   
   

