# Introduction

**Apache Cassandra** is a highly scalable, high-performance distributed database designed to handle large amounts of data, providing high availability with no single point of failure. It's a **column-based** NoSQL database created by **Meta** (ex. **Facebook**)

* **Features**:

	* Highly and Linearly Scalable
	* No Single Point of Failure (i.e., no single part of the system can stop the entire system from working)
	* Quick Response Time
	* Flexible Data Storage (i.e., supports structured, unstructured, and semi-structured data)
	* Easy Data Distribution (i.e., supports flexible data duplication)
	* BASE Properties
	* Fast Writes

* **CQL**:

	* To query the data stored within Cassandra, a dedicated query language named **Cassandra Query Language (CQL)** was developed
	* **CQL** offers a model similar to **MySQL** under many different aspects
		* It is used to query data stored in **tables**
		* Each table is made by **rows** and **columns**
		* Most of the **operators** are the ones used in MySQL

	* **CQL** commands and queries can either be run in the console or by reading a textual file with the corresponding command



# Syntax

## `CREATE KEYSPACE`

* The first operation to perform before creating the table is creating the **keyspace**

	* **Keyspace** is equivalent to a "database" in relational databases
	* A **keyspace** is the outermost container in Cassandra

* Keyspaces are created using the **`CREATE KEYSPACE`** command

	```sql
	cqlsh> CREATE KEYSPACE <identifier> WITH <properties>
	```

	* `<identifier>` : The name of your keyspace
	* `<properties>` : Settings like replication strategy and factor

* Let's create a keyspace with the name **population**

	```sql
	cqlsh> CREATE KEYSPACE population
		   WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 3};
	```



## Partition & Clustering Key

* A table can employ many different **Clustering** and/or **Partition Keys**

	* The **Clustering Key** determines the sort order of row within a partition. It controls how data is organised and retrieved within the same **Partition Key**
	* The **Partition Key** determines how data is distributed across nodes in a Cassandra cluster. Rows with the same **Partition Key** are stored together in the same node, which improves data locality

	```CQL
	PRIMARY KEY ((personal_id, ...), -- Partition Keys 
	              age, ...) -- Clustering Keys
	```

	* Data is partitioned by `personal_id, ...`
	* Within each partition, rows are sorted by `age, ...`

* When creating a table, clustering keys can be used to define an ordering

	```CQL
	cqlsh:population> CREATE TABLE person (...)
					  WITH CLUSTERING ORDER BY (age ASC, ...);
	```

	* `age ASC` specifies that rows within a partition will be sorted in ascending order by age



## `DESCRIBE` & `USE`

* The **`DESCRIBE`** command can be used to check whether a keyspace (or a table) has been correctly created. It can also be applied to other elements

	```CQL
	cqlsh> DESCRIBE keyspaces;
	```

	* This command lists all the keyspaces in the Cassandra cluster

* To be able to perform the operations on the tables (that we still have to create), we must choose in which keyspace we want to work. The command **`USE`** covers such need

	```CQL
	cqlsh> USE <keyspace_name>;
	```

* Let's **`USE`** the keyspace we just created

	```CQL
	cqlsh> USE population;
	```



## `ALTER` & `DROP`

* Keyspaces can be also modified ( **`ALTER`** ) and deleted ( **`DROP`** ) with the corresponding commands

	```CQL
	cqlsh> ALTER KEYSPACE <identifier> WITH <properties>;
	```

	```CQL
	cqlsh> DROP KEYSPACE <identifier>;
	```



## `CREATE` a Table

* Let's now learn how to **`CREATE`** a table, the command is the following:

	```CQL
	cqlsh:keyspace> CREATE TABLE <table_name> (
						<column_definition>,
	    				<column_definition>,
	    				...
					)
	```

	* Optionally, some options can be included by using **`WITH <options>`**
	* **`<column_definition>`** is performed as **`<column_name> <column_type>`**

* Let's try to create a simple table named **person** with name, age, birth date and gender

	```CQL
	cqlsh:population> CREATE TABLE person (
					      personal_id text,
	    				  name text,
	    				  age varint,
	    				  birth_date text,
	    				  gender text,
	    				  PRIMARY KEY (personal_id, age)
					  );
	```

* Let's check whether the table has been created or not

	```CQL
	cqlsh:population> DESCRIBE tables;
	```

	* This command lists all tables in the current keyspace. If **person** appears in the output, the table has been successfully created

* Now that the table has been created, let's take a look at its description

	```CQL
	cqlsh:population> DESCRIBE person;
	```

	* This command prints a lot of details about the table. All of these details can be customized when creating the table (as mentioned before)



## `ALTER` a Table

* Table can be also modified through the **`ALTER`** command

	```CQL
	cqlsh:keyspace> ALTER TABLE <table_name> <instructions>;
	```

* For example, we can add a new column to our table

	```CQL
	cqlsh:keyspace> ALTER TABLE <table_name> ADD <column_definition>;
	```

	Or remove a column from it

	```CQL
	cqlsh:keyspace> ALTER TABLE <table_name> DROP <column_name>;
	```

* Let's try add two new columns to the person table named address (text) and salary (float)

	```CQL
	cqlsh:population> ALTER TABLE person
					  ADD address text;
	```

	```CQL
	cqlsh:population> ALTER TABLE person
					  ADD salary float;
	```

* Let's drop the salary attribute from the person table

	```CQL
	cqlsh:population> ALTER TABLE person
					  DROP salary;
	```



## `DROP` & `TRUNCATE` a Table

* Tables can be also deleted through the **`DROP`** command

	```CQL
	cqlsh:keyspace> DROP TABLE <table_name>;
	```

* Rather than deleting the table, it is possible to empty it through the **`TRUNCATE`** command

	```CQL
	cqlsh:keyspace> TRUNCATE TABLE <table_name>;
	```

* **N.B.** by looking at the documentation, you may notice that the keyword **`TABLE`** can be interchanged with **`COLUMNFAMILY`**, there is no difference between them



## `CREATE INDEX`

* **Indexes** are one of the most important elements of a table in Cassandra. They allow to query the column efficiently. It is kind of hard to notice such an advantage on a small set of data, while it is essential in big datasets

* **Secondary Indexes** are created with the following command

	```CQL
	cqlsh:keyspace> CREATE INDEX <identifier>
					ON <table_name> (<column_name>);
	```

* Let's create an index on the column name of the table person

	```CQL
	cqlsh:population> CREATE INDEX person_name
					  ON person (name);
	```



## `DROP INDEX`

* Indexes can also be deleted through the **`DROP`** command

	```CQL
	cqlsh:keyspace> DROP INDEX index_name
	```

* Let's add a new index on the address column of the table person

	```CQL
	cqlsh:population> CREATE INDEX person_address
					  ON person (address)
	```

	then remove it

	```CQL
	cqlsh:population> DROP INDEX person_address
	```



## `INSERT` Data

* Let's see how to **`INSERT`** data within our tables

	```CQL
	cqlsh:keyspace> INSERT INTO <tablename> (<column_name1>, <column_name2>, ...)
					VALUES (<column_value1>, <column_value2>, ...)
	                USING <option>;
	```

* Let's try and insert a new person in our table

	```CQL
	cqlsh:population> INSERT INTO person(personal_id, address, age, birth_date, 										 gender, name)
					  VALUES('FRNTRZ95E12F675T', 'Via Milano 12', 26, '12-05-1995', 						 'Male', 'Francesco Terzani');
	```



## `SELECT` Data

* Let's see how to **`SELECT`** the data within our tables

	```CQL
	cqlsh:keyspace> SELECT <field_list>
					FROM <table_name>
					WHERE <conditions>
	```

* Let's select the person we just inserted within or database using their `personal_id`

	```CQL
	cqlsh:population> SELECT *
					  FROM person
					  WHERE personal_id = 'FRNTRZ95E12F675T'
	```

* Let's retrieve the person we inserted within our database through their age

	```CQL
	cqlsh:population> SELECT *
					  FROM person
					  WHERE age = 26
	```

	An <font color=red>**Invalid Request Error**</font> is shown as the age column has no associated primary or secondary index! Indeed, being Cassandra a column-oriented database, all the operations are optimized to extract data from columns. To solve this issue, it's necessary to query with respect to the attributes included in the **primary key** or to create a **secondary index**. Be careful that not all the operations are supported (e.g., most comparison operators needs the additional statement **`ALLOW FILTERING`**)

	* **Use Primary or Secondary Index**:

		* Create a secondary index on the age column:

			```CQL
			CREATE INDEX age_index ON person(age);
			```

		* After creating the index, the query will work:

			```CQL
			SELECT *
			FROM person
			WHERE age = 26;
			```

	* **Allow Filtering**:

		* Add the `ALLOW FILTERING` clause to force Cassandra to execute the query:

			```CQL
			SELECT *
			FROM person
			WHERE age = 26 ALLOW FILTERING;
			```



## `UPDATE` Data

* Let's see how to **`UPDATE`** tuples within our database

	```CQL
	cqlsh:keyspace> UPDATE <table_name>
					SET <column_name> = <new_value>, ...
					WHERE <condition>;
	```

* Let's update Francesco's address to 'Via Milani 13'

	```CQL
	cqlsh:population> UPDATE person
					  SET address = 'Via Milani 13'
					  WHERE personal_id = 'FRNTRZ95E12F675T';
	```



## `DELETE` Data

* Let's see how to **`DELETE`** the data from our tables

	```CQL
	cqlsh:keyspace> DELETE
					FROM <table_name>
					WHERE <condition>;
	```

* Let's try to deleting Francesco using their address

	```CQL
	cqlsh:population> DELETE
					  FROM person
					  WHERE address = 'Via Milani 13';
	```

	An <font color=red>**Invalid Query Error**</font> is displayed as we are not performing a **`DELETE`** operation using a primary key, which is against Cassandra's standard operations pattern

* Let's perform a proper **`DELETE`** operation using the primary key

	```CQL
	cqlsh:population> DELETE
					  FROM person
					  WHERE personal_id = 'FRNTRZ95E12F675T';
	```

* Let's check whether our operation was successfully performed

	```CQL
	cqlsh:population> SELECT * FROM person
	```



## `BATCH`

* A set of **`INSERT`**, **`UPDATE`** and **`DELETE`** operations can be organized in **`BATCH`**. In that way, they are executed one after another with a single command

	```CQL
	cqlsh:keyspace> BEGIN BATCH
						<insert_statement>;
						<update_statement>;
						<delete_statement>;
	                APPLY BATCH;
	```



## `CAPTURE`

* When the amount of data within a database grows, it can be really tough to visualise it within a terminal. Fortunately, Cassandra provides us with a few commands to overcome this problem

* The **`CAPTURE`** command followed by the path of the folder in which store the results and the name of the file

	```CQL
	cqlsh> CAPTURE D:/Program Files/Cassandra/Outputs/output.txt;
	```

	* `D:/Program Files/Cassandra/Outputs/output.txt` : The full path to the file where you want to save the output, including the file name

* To interrupt the **`CAPTURE`** you can run the following command

	```CQL
	cqlsh> CAPTURE off;
	```

	* The command interrupts the current capture session, and query results will no longer be saved to the file



## `EXPAND`

* The **`EXPAND`** command provides extended outputs within the console when performing queries. It must be executed before the query to enable it

	```CQL
	cqlsh> EXPAND on;
	```

* To interrupt the **`EXPAND`** you can run the following command

	```CQL
	cqlsh> EXPAND off;
	```



## `SOURCE`

* The **`SOURCE`** command allows you to run queries from textual files. The command accepts the path to the file with the query

	```CQL
	cqlsh> SOURCE D:/Program Files/Cassandra/Queries/query_1.txt;
	```



# Foreign Key

==In Cassandra there is no concept of  **Foreign Keys** and/or relationships,== if you want any cross-table check to be performed, you have to manage it by yourself

**N.B.** As mentioned before, Cassandra wasn't created to perform such operations, but to be able to query a lot of data quickly and efficiently. If such operations are needed, you'd better reconsider your DB choice



# Data Types

* Cassandra supports many different data types, like text, variant, float, double, Boolean, etc
* In particular, it supports two particular data types
	* Collections
	* User-defined data types



## Collections

* Collections are pretty easy to define and update

	```CQL
	cqlsh:keyspace> CREATE TABLE test(email list<text>, ...)
	```

	* Creates a list column named email that can store multiple email addresses

	```CQL
	cqlsh:keyspace> UPDATE test 
					SET email = email + [...] 
					WHERE ...
	```

	* Adds an email address to the email list



## User-defined Data Types

* When it comes to user-defined data types the complexity increases, as it is necessary to define the data type before using it

	```CQL
	cqlsh:keyspace> CREATE TYPE <type_name> (
						<column_definition>
	    				...
					)
	```

* To check that the new type has been properly created, you can use the **`DESCRIBE`** operator. User-defined data types support the **`ALTER`** and **`DROP`** operations

	```CQL
	cqlsh:keyspace> DESCRIBE TYPE <type_name>
	```

	

# Exercises

Create a keyspace named `car_dealer`

```CQL
cqlsh> CREATE KEYSPACE car_dealer
	   WITH replication = {'class': 'SimpleStrategy', 
	   					   'replication_factor': 3};
```



Check the existence of the keyspace

```CQL
cqlsh> DESCRIBE keyspaces;
```

```CQL
cqlsh> DESCRIBE car_dealer;
```



Create a table named `car` within the keyspace with the following attributes `car_id` (uuid, primary key), `brand` (textual), `max_speed` (integer), `price` (float), `consumption_lt_per_km` (float) and sorted by `max_speed` in `ascending` order

```CQL
cqlsh:car_dealer> CREATE TABLE Car (
				      car_id uuid,
    				  brand text,
    				  max_speed varint,
    				  price float,
    				  consumption_lt_per_km float,
    				  PRIMARY KEY (car_id))
				  WITH CLUSTERING ORDER BY (max_speed ASC);
```



Add a new column to the table, named `features` that contains the set/list of features of the cars (e.g., air conditioning, etc.). Each `feature` is made of name and description

```CQL
cqlsh:car_dealer> CREATE TYPE feature (
				  	  name text,
    				  description text);
```

```CQL
cqlsh:car_dealer> ALTER TABLE car
				  ADD features list<frozen<feature>>;
```

* When using a user-defined data type, it is necessary to use the ==**frozen**== keywords. A frozen data type can only be overwritten, it can't edited anymore



As we do not want to deal with complex fields, let's remove the `features` field

```CQL
cqlsh:car_dealer> ALTER TABLE car
				  DROP features;
```



Insert a new value in the table (use the function `uuid()` to get a unique identifier)

```CQL
cqlsh:car_dealer> INSERT INTO car(car_id, brand, max_speed, price, consumption_lt_per_km)
				  VALUES (uuid(), 'Ferrari', 320, 200000.00, 30.00);
```



Run the data creation operations from the `car_data.txt` file

```CQL
cqlsh:car_dealer> SOURCE '<path_to_your_folder>/car_date.txt';
```



Check that all the data have been properly uploaded

```CQL
cqlsh:car_dealer> SELECT * FROM car;
```



Extract all the cars that cost more than 100000

```CQL
cqlsh:car_dealer> SELECT * FROM car WHERE price > 100000 
				  ALLOW FILTERING;
```



Extract all the cars that cost exactly 35000 (without using `ALLOW FILTERING`)

```CQL
-- we have to create index first if without using ALLOW FILTERING and without index on car price
cqlsh:car_dealer> CREATE INDEX car_price ON car(price);
cqlsh:car_dealer> SELECT * FROM car WHERE price = 35000;
```



Extract the sum of all the prices of the cars in the DB

```CQL
cqlsh:car_dealer> SELECT SUM(price) FROM car;
```



Count the number of Ferrari cars in the DB and store it in a file named `ferrari_count.txt`

```CQL
cqlsh:car_dealer> CAPTURE <path_to_folder>/ferrari_count.txt;
cqlsh:car_dealer> CREATE INDEX car_brand ON car(brand);
cqlsh:car_dealer> SELECT COUNT(*) FROM car WHERE brand = 'Ferrari';
cqlsh:car_dealer> CAPTURE off;
```





