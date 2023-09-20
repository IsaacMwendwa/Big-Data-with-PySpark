# Introduction to PySpark

## Getting to Know PySpark

### Using Spark in Python
* The first step in using Spark is connecting to a cluster
* In practice, the cluster will be hosted on a remote machine that's connected to all other nodes
* There will be one computer, called the master that manages splitting up the data and the computations
* The master is connected to the rest of the computers in the cluster, which are called worker. The master sends the workers data and calculations to run, and they send their results back to the master
* When you're just getting started with Spark it's simpler to just run a cluster locally. Thus, for this course, instead of connecting to another computer, all computations will be run on DataCamp's servers in a simulated cluster
* Creating the connection is as simple as creating an instance of the SparkContext class. The class constructor takes a few optional arguments that allow you to specify the attributes of the cluster you're connecting to
* An object holding all these attributes can be created with the SparkConf() constructor. Take a look at the documentation for all the details!
* For the rest of this course you'll have a SparkContext called sc already available in your workspace.

### Using DataFrames
Spark's core data structure is the Resilient Distributed Dataset (RDD). This is a low level object that lets Spark work its magic by splitting data across multiple nodes in the cluster. However, RDDs are hard to work with directly, so in this course you'll be using the Spark DataFrame abstraction built on top of RDDs.

The Spark DataFrame was designed to behave a lot like a SQL table (a table with variables in the columns and observations in the rows). Not only are they easier to understand, DataFrames are also more optimized for complicated operations than RDDs.

When you start modifying and combining columns and rows of data, there are many ways to arrive at the same result, but some often take much longer than others. When using RDDs, it's up to the data scientist to figure out the right way to optimize the query, but the DataFrame implementation has much of this optimization built in!

To start working with Spark DataFrames, you first have to create a SparkSession object from your SparkContext. You can think of the SparkContext as your connection to the cluster and the SparkSession as your interface with that connection.

![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/6d40484b-bb15-4320-b815-7ca0a0409bbe)

* To list the names of all tables in your cluster as a list: `print(spark.catalog.listTables())`
* One of the advantages of the DataFrame interface is that you can run SQL queries on the tables in your Spark cluster
* ![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/f8b7d96a-23da-42ef-a274-4d822b6c37bc)

* To Pandafy a Spark DataFrame
* ![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/5000b365-e992-4580-9b00-ea7a6741a8c6)

### Adding Data From Spark DataFrame to Spark Cluster
The .createDataFrame() method takes a pandas DataFrame and returns a Spark DataFrame.

The output of this method is stored locally, not in the SparkSession catalog. This means that you can use all the Spark DataFrame methods on it, but you can't access the data in other contexts. For example, a SQL query (using the .sql() method) that references your DataFrame will throw an error. To access the data in this way, you have to save it as a temporary table.

You can do this using the .createTempView() Spark DataFrame method, which takes as its only argument the name of the temporary table you'd like to register. This method registers the DataFrame as a table in the catalog, but as this table is temporary, it can only be accessed from the specific SparkSession used to create the Spark DataFrame.

There is also the method .createOrReplaceTempView(). This safely creates a new temporary table if nothing was there before, or updates an existing table if one was already defined. You'll use this method to avoid running into problems with duplicate tables.

Check out the diagram to see all the different ways your Spark data structures interact with each other.
![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/8bc551e5-8fac-495e-8b1b-2f7666718eca)

* Sample code:
  * ![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/2b5be9d1-d09c-49b8-87ba-c410d287463e)

### Reading CSV files in Spark
![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/331242c6-f471-429f-9005-57ab59816efc)


## Manipulating Data
In this chapter, you'll learn how to use the methods defined by Spark's DataFrame class to perform common data operations. Let's look at performing column-wise operations. 

In Spark you can do this using the .withColumn() method, which takes two arguments. First, a string with the name of your new column, and second the new column itself. The new column must be an object of class Column. Creating one of these is as easy as extracting a column from your DataFrame using df.colName.

Updating a Spark DataFrame is somewhat different than working in pandas because the Spark DataFrame is immutable. This means that it can't be changed, and so columns can't be updated in place. Thus, all these methods return a new DataFrame. To overwrite the original DataFrame you must reassign the returned DataFrame using the method like so:

`df = df.withColumn("newCol", df.oldCol + 1)`

The above code creates a DataFrame with the same columns as df plus a new column, newCol, where every entry is equal to the corresponding entry from oldCol, plus one. To overwrite an existing column, just pass the name of the column as the first argument!

![image](https://github.com/IsaacMwendwa/Big-Data-with-PySpark/assets/51324520/019fb9c1-aec2-4bec-8fe9-0a60308bf8e3)