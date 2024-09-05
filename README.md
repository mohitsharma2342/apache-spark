# Hadoop : 
there are main two components of hadoop
    1.hdfs 
	  2.MapReduce 

Limitations :
Although hadoop is very good to handle the bigdata 
one of the relied on storing data on disks which made things slower every we run  a job  it store the data on the disc ,read the data and process
which made the   processing  a bit slower 
Another issue with hadoop it process the only in batches this means we have to wait for the one process to complete before submiiting any other job 

So there was a need to process all of this data faster and in real time here's where apache spark comes in the picture 
in spark they introduces the concept of RDD resilient distributed datasets it allows the data to store in memory and enables faster data access and processing 
instead of reading and wrinting data repeatedly from  disk ,Spark processes the entire data in just memmory 
in memory data processing of data makes sparks 100x faster then hadoop 
And also comes with different components .

https://youtube.com/shorts/wxzYJTNK878?si=FaTpyvicwWLjFEoU

Apark works on master slave architecture so basically Apache spark manages and cordinates the execution of tasks on data across a called cluster manager
So whenever we write any code in spark its called Spark application whenever we run anything it goes to cluster manager which grants resoureces to all applications 


# Tranformation and Action 
   Tranformation are divided into two types
    1.Narrow Dependency : Tranformation that doesn't require data movement between partitions (filter , map ,select etc)
    2 Wide Dependency  :  Tranformation that  require data movement between partitions (joins ,groupby ,reduceBykey )

# DAG :
  Dag is basically directed acyclic graph it basically gives you the entire computation workflow its basically used for the 
  scheduling and perfomance optimization .so when we called the action then DAG gets invoked .
	so there will be logical plan ,pysical plan and perfomance plan 


# apache-spark

The main difference between window functions and aggregate functions is that aggregate functions group multiple rows into a single result row; all the 
individual rows in the group are collapsed and their individual data is not shown. On the other hand, window functions produce a result for each individual row. 
This result is usually shown as a new column value in every row within the window

# JOINS

Brodcast join  : It is an optimization technique used to improve the performance of join operations .This technique can significantly improve the performance of joins when one of the datasets is small enough to fit into the memory of each worker node.
                 Spark shuffle large amounts of data across the network when we use join , which can be a costly operation. so by this we can broadcasting the smaller dataset to all worker nodes,

Important Considerations
**Size of the Smaller Dataset**: Ensure that the smaller dataset is indeed small enough to be broadcasted to all worker nodes without causing memory issues.
**Cluster Resources**: Adequate resources (memory) must be available on each worker node to handle the broadcasted dataset.
**Use Cases**: Broadcast joins are particularly useful in star schema joins, where a large fact table is joined with small dimension tables.

# Perform broadcast join
result_df = large_df.join(broadcast(small_df), "common_column")

Sort-Merge Join:
Description: This is the default join strategy in Spark for large datasets. Both DataFrames are sorted by the join key, and then the sorted data is merged.
Usage: This is automatically chosen by Spark when the data is large, and it is not explicitly specified by the user.

Shuffle Hash Join:
Description: This join strategy is used when one of the DataFrames is much smaller than the other. The smaller DataFrame is used to build a hash table, and the larger DataFrame is shuffled across the cluster to perform the join.
Usage: This is automatically chosen by Spark based on the size of the DataFrames and is not explicitly specified by the user.

Broadcast Hash Join:
Description: Similar to broadcast join, but specifically when the smaller DataFrame is broadcast and a hash table is built on the join key.
Usage: This is automatically chosen by Spark when the smaller DataFrame is small enough to be broadcasted and a hash join is preferred.

# Cache vs persist
In Apache Spark, both cache() and persist() are used to store DataFrames or RDDs in memory to speed up future computations. While they serve similar purposes, there are key differences in their usage and functionality.

cache()
Default Storage Level: By default, cache() stores the DataFrame or RDD in memory only (MEMORY_ONLY).
Simplicity: It's a shorthand method for persist() with the default storage level.
Use Case: Use cache() when you are certain that the DataFrame or RDD can fit into memory and you don't need any specific storage level beyond the default.
Example:

df.cache()
df.count()  # Action to trigger the caching
persist()
Customizable Storage Levels: persist() allows you to specify different storage levels, such as storing data in memory, on disk, or both, and whether to use serialization.

Storage Levels:
MEMORY_ONLY: Store in memory (default for cache()).

MEMORY_AND_DISK: Store in memory and spill to disk if there is not enough memory.

MEMORY_ONLY_SER: Store in memory with serialization.

MEMORY_AND_DISK_SER: Store in memory with serialization and spill to disk if needed.

DISK_ONLY: Store only on disk.
OFF_HEAP: Store in off-heap memory (requires special configuration).

# Partitioning and Bucketing
Partitioning: Divides data into partitions based on the values of one or more columns. Useful for improving query performance by skipping entire partitions.
Bucketing: Divides data into a fixed number of buckets based on the hash of a column. Useful for efficient joins and aggregations. by ensuring that the data in each bucket is evenly distributed and easily accessible.
Combining Both: Can be used together to optimize data storage and query performance even further.

Combining Partitioning and Bucketing
You can combine partitioning and bucketing to optimize data storage and query performance further. This approach leverages the strengths of both techniques, partitioning data based on high-level categories and bucketing within those partitions for finer-grained management.

df.write.partitionBy("department").bucketBy(4, "id").sortBy("name").saveAsTable("partitioned_and_bucketed_table")

Executor Memomry 
  Suppose we have give 10gb to executor so it 10% to memoery to memoryOverhead 
   so 10% of 10gb is 1gb 


Lineage Graph:-
A lineage graph is a visual representation of the data flow and transformations across systems or processes. 
It is commonly used in data management and analytics to track the origin, transformations, and destinations of data as it moves through various stages. 
Lineage graphs help to understand how data is produced, modified, and consumed, providing transparency and traceability.
-lineage graphs show relationships between data sources, intermediate processes, and final outputs, making it easier to:


In Spark, when you perform operations on an RDD (Resilient Distributed Dataset), Spark does not execute them immediately. Instead, it builds a DAG (Directed Acyclic Graph) of transformations.
Lineage graph in Spark is the logical representation of the transformations applied to RDDs.
It helps with fault tolerance, as Spark can recompute lost data based on lineage.
The DAG of operations is used by Spark to optimize execution and recover from failures.

When an action like collect(), save(), or count() is called, Spark submits the DAG to the DAG Scheduler to execute the tasks in the correct order.
Spark can recover from node failures by recomputing lost partitions using the lineage information, instead of reloading the entire dataset from scratch.