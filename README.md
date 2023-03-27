# 8 Vs of BDA

Volume, Velocity, Variety, Veracity, Value, Visualization, Viscosity, Virality

# HDFS Architecture

HDFS (Hadoop Distributed File System) is the underlying file system of Hadoop. It is a distributed file system designed to store large data sets across multiple commodity servers, providing high throughput access to this data.
· Individual files are broken into blocks of fixed size (typically 256 MB blocks)
· Stored across cluster of nodes (not necessarily on same machine)
· These files can be more than the size of individual machine's hard drive
· So access to a file requires cooperation of several nodes
The HDFS architecture consists of:
· NameNode: The master node that manages the file system namespace and regulates access to files by clients. Name Node is controller and manager of HDFS. It knows the status and the metadata of all the files in HDFS. HDFS cluster can be accessed concurrently by multiple clients, even then this metadata information is never desynchronized; hence, all this information is handled by a single machine. Since metadata is typically small, all this info is stored in main memory of Name Node, allowing fast access to metadata
· DataNode: The slave nodes that store the actual data blocks. It knows only about the data stored on it and will read data and send to client when retrieval requested. It will receive data and store locally when storage is requested.
· Secondary NameNode: An auxiliary node that helps the NameNode manage its file system metadata, including the namespace and file-to-block mapping. It is not backup of name node nor data nodes connect to this; it is just a helper of name node. It only performs periodic checkpoints. It communicates with name node and to take snapshots of HDFS metadata. These snapshots help minimize downtime and loss of data
· Client: The component that interacts with HDFS, submits read/write requests, and receives responses.
In HDFS, data is split into blocks and distributed across multiple DataNodes for storage. The NameNode maintains metadata about the locations of the blocks, enabling it to serve clients' read/write requests and manage data replication across the cluster.

# HDFS Read

Here are the steps involved in reading a file from HDFS:
1. Client sends a read request to the NameNode for the desired file.
2. NameNode responds with the block location information for the requested file.
3. Client establishes a connection with the DataNode that holds the first block of the file.
4. Client retrieves the block of data from the DataNode.
5. If the file is stored in multiple blocks, the client repeats steps 3 and 4 for each remaining block.
6. The client reassembles the retrieved blocks into the original file.
7. The client receives the requested file from HDFS.
In HDFS, data is stored in a redundant manner across multiple DataNodes to ensure reliability and availability. The NameNode is responsible for ensuring that data is available even in the case of a node failure.

# HDFS Write
Here are the steps involved in writing a file to HDFS:
1. Client creates a file and opens it for writing.
2. Client splits the file into blocks and stores each block in a local buffer.
3. Client sends a write request to the NameNode to create a new file in HDFS.
4. NameNode returns a list of DataNodes where the blocks of the file should be stored.
5. Client writes the first block of the file to the first DataNode in the list.
6. Client repeats step 5 for each remaining block, writing the blocks to their corresponding DataNodes.
7. The DataNodes acknowledge the receipt of each block and store it locally.
8. The NameNode updates its metadata to reflect the addition of the new file and the locations of its blocks.
9. Client closes the file, completing the write process.
In HDFS, data is stored in a redundant manner across multiple DataNodes to ensure reliability and availability. The NameNode is responsible for ensuring that data is available even in the case of a node failure by replicating blocks across the cluster.

# Map Reduce in Action
MapReduce is a programming model and an associated implementation for processing and generating large data sets with a parallel, distributed algorithm on a cluster.
In MapReduce, the data is first divided into smaller chunks, and then a map function is applied to each chunk in parallel, transforming the data into intermediate key-value pairs. These intermediate pairs are then sorted and grouped by key, and a reduce function is applied to each group, aggregating the values into a final output.
Here's a simple example to help illustrate the process:
Suppose we have a large dataset of text documents and we want to count the number of occurrences of each word in the documents.
· The map function takes as input a single document and outputs a series of key-value pairs, where each pair consists of a word from the document and the value 1.
· The reduce function takes as input a word and a list of its associated values (1s), and outputs a single key-value pair, where the key is the word and the value is the total number of occurrences of the word in the input data.
· The MapReduce framework takes care of dividing the data into chunks, distributing the map and reduce operations to multiple machines, and combining the intermediate results into a final output.
This example demonstrates how MapReduce can be used to perform complex data processing and aggregation tasks in a parallel and scalable manner. The model can be applied to a wide range of problems, from counting the frequency of words in text to calculating statistics on large datasets.
MAP REDUCE STEPS
MapReduce is a programming model for processing and generating large data sets with a parallel, distributed algorithm on a cluster. The following are the steps involved in a MapReduce job:
1. Input: The input data is divided/ splited into smaller chunks and distributed across the cluster.
2. Job needs to be scheduled to carry out required process
3. Schedule tasks on nodes where data is already present
4. Map: A map function is applied to each chunk of data in parallel, producing a set of intermediate key-value pairs.
5. Shuffle and Sort: The intermediate key-value pairs are sorted and grouped by key, producing a set of key-value lists.
6. Reduce: A reduce function is applied to each group of intermediate key-value pairs, aggregating the values into a final output.
7. Output: The final output is collected and stored in the desired format.
The MapReduce framework takes care of managing the distribution of data and tasks across the cluster, as well as handling failures and network communication. This allows developers to focus on writing the map and reduce functions, while the framework provides the underlying infrastructure for parallel and scalable processing.

# MRv1
MRv1, short for MapReduce Version 1, is an earlier implementation of the MapReduce programming model for processing and generating large data sets with a parallel, distributed algorithm on a cluster. It was the original implementation of MapReduce and was part of the Hadoop ecosystem.
In MRv1, the processing was managed by a single component called the JobTracker, which was responsible for scheduling and coordinating MapReduce tasks on a cluster of worker nodes. The JobTracker would also monitor the health of the tasks and manage the distribution of data to ensure optimal processing.
MRv1 had a number of limitations, including a single point of failure (the JobTracker), poor scalability, and limited resource management capabilities.
MRv1 Process:
· A Client invokes a Map Reduce, from a Calling Node (maybe a Data Node or an Extra Node in the cluster)
· An instance of Job Tracker is created in the memory of the Calling Node
· The Job Tracker queries the Name Node and finds the Data Nodes (location of the data to be used)
· Job Tracker then launches Task Trackers in the memory of all the Data Nodes as above to run the jobs
· Job Tracker gives the code to Task Tracker to run as a Task
· Task Tracker is responsible for creating the tasks & running the tasks
· In effect the Mapper of the Job is found here
· Once the Task is completed, the result from the Tasks is sent back to the Job Tracker
· Job Tracker also keeps a track of progress by each Task Tracker
· The Job Tracker also receives the results from each Task Tracker and aggregates the results
· In effect the Reducer of the Job is found here

# MRv2
MRv2, short for MapReduce Version 2, is a more recent implementation of the MapReduce programming model for processing and generating large data sets with a parallel, distributed algorithm on a cluster.
MRv2, also known as YARN (Yet Another Resource Negotiator), introduced a more flexible and scalable architecture for processing large data sets. Unlike MRv1, which had a single component (the JobTracker) managing the processing, MRv2 separates the processing management into two components: the Resource Manager and the Application Master.
The Resource Manager is responsible for allocating resources (such as CPU and memory) to running applications and managing the allocation of containers (units of processing resources). The Application Master is responsible for negotiating resources from the Resource Manager, tracking the progress of the tasks, and responding to failures.
With MRv2, multiple applications can run on the same cluster simultaneously, each with its own Application Master. This provides improved scalability and more advanced resource management capabilities compared to MRv1.

MRv2 Process:
· A Client invokes a Map Reduce, from a Calling Node (maybe a Data Node or an Extra Node in the cluster)
· An instance of Resource Manager is created in the memory of the Calling Node
· The Resource Manager then launches containers with appropriate resources (memory) with App Node Manager in memory of the Calling Node
· Along with this Application Master is invoked. Application Master is “pause” mode till all containers
· With Task Node Manager (as below) are created
· The Resource Manager queries the Name Node and finds the Data Nodes (location of the data used)
· The Resource Manager then launches containers with appropriate resources (memory) with Task Node Manager in all the Data Nodes as above to run the jobs
· Application Master gives the code to Task Node Manager to run as a Task
· Task Node Manager is responsible for creating & running tasks. In effect the Mapper of the Job is here
· Once the Task is completed, the result from the Tasks is sent back to the Application Master
· Application Master also keeps a track of progress by each Task Node Manager
· The Application Master also receives the results from each Task Node Manager and aggregates the results
· In effect the Reducer of the Job is found here
· Thus, from previous version, Job Tracker has been replaced by Resource Manager & Application Master
· From previous version, Task Tracker has been replaced by Task Node Managers

# Difference Between MRv1 and MRv2


# Map Reduce failure and recovery
MRv1
· The processing was managed by a single component called the JobTracker. The JobTracker was responsible for scheduling and coordinating MapReduce tasks on a cluster of worker nodes.
· In case of a failure of a task or a node, the JobTracker would detect the failure and reschedule the task on another node.
· If the JobTracker failed, all running MapReduce jobs would be lost, and the cluster would become unavailable until the JobTracker was manually restarted.

MRv2

· If a task or node fails, the Application Master can detect the failure and reschedule the task on another node.
· The Resource Manager can also monitor the health of the cluster and take corrective actions in case of failures.
· Additionally, MRv2 allows for multiple applications to run on the same cluster simultaneously, so if one application experiences a failure, the other applications can continue to run without interruption.
· The data processed is also stored in a highly-reliable and scalable storage system, such as HDFS, which provides built-in mechanisms for data replication and recovery.
· Ensure that the data remains safe and accessible even in case of node failures.

# Apache Hive Architecture

Hive is a data warehouse system for Hadoop. It is used to run SQL like queries as Map Reduce jobs.

Hive architecture components: 
User Interface – User submits queries through this. 3 ways of submitting queries: HIVE CLI, Web Interface and Thrift server.
Driver – Receives the queries from the user interface. Tasks performed - Fetches the required APIs needed for the queries. Converts Hive queries into Map Reduce programs. 
Compiler – Plays a role in converting the Hive queries in Map Reduce programs and also generates the execution plan of the program with help from Metastore
Metastore – A small database containing information about the tables, partitions etc.
Execution Engine – Connected to the Hadoop framework. It executes the plan created by the compiler. Interacts with the name node and resource manager to fetch the desired output data.


# Apache Spark

Apache Spark is a data processing framework that can quickly perform processing tasks on very large datasets. It can also distribute these data processing tasks across multiple computers.
The RDD (Resilient Distributed Dataset) is the Spark's core abstraction. It is a collection of elements, partitioned across the nodes of the cluster so that we can execute various parallel operations on it.
There are two ways to create RDDs:
o	Parallelizing an existing data in the driver program
o	Referencing a dataset in an external storage system (HDFS, HBASE, etc)

Driver Program: Runs the main() function of the program and creates the SparkContext object.
SparkContext: Used to connect to the spark clusters. 
The Spark Driver is responsible for converting the user-written code into jobs that are executed on the clusters.
SparkContext performs the following:
1.	Acquire executor on nodes in the cluster
2.	Send application code to the executors
3.	Send tasks to the executors to run

Cluster Manager: Allocates resources across applications.
Worker Node: Slave node that is used to run the application code in the clusters.
Executor: It is a JVM process that is used to execute job in the worker nodes.


# Difference and similarities btw Hadoop and Spark
Both are big data infrastructure frameworks used to store and process large datasets. 

HDFS uses MapReduce to process and analyze data. 

Processing Speed & Performance	Hadoop’s MapReduce model reads and writes from a disk, thus slowing down the processing speed.	Spark reduces the number of read/write cycles to disk and stores intermediate data in memory, hence faster-processing speed.
Usage	Hadoop is designed to handle batch processing efficiently.	Spark is designed to handle real-time data efficiently.
Latency	Hadoop is a high latency computing framework, which does not have an interactive mode.	Spark is a low latency computing and can process data interactively.
Data 	With Hadoop MapReduce, a developer can only process data in batch mode only.	Spark can process real-time data, from real-time events like Twitter, and Facebook.
Cost	Hadoop is a cheaper option available while comparing it in terms of cost	Spark requires a lot of RAM to run in-memory, thus increasing the cluster and hence cost.
Algorithm Used	 The PageRank algorithm is used in Hadoop.	Graph computation library called GraphX is used by Spark.
Fault Tolerance	Hadoop is a highly fault-tolerant system where Fault-tolerance achieved by replicating blocks of data. 
If a node goes down, the data can be found on another node	Fault-tolerance achieved by storing chain of transformations
If data is lost, the chain of transformations can be recomputed on the original data
Security	Hadoop supports LDAP, ACLs, SLAs, etc and hence it is extremely secure.	Spark is not secure, it relies on the integration with Hadoop to achieve the necessary security level. 
Machine Learning	Data fragments in Hadoop can be too large and can create bottlenecks. Thus, it is slower than Spark.	Spark is much faster as it uses MLib for computations and has in-memory processing.
Scalability	Hadoop is easily scalable by adding nodes and disk for storage. It supports tens of thousands of nodes.	It is quite difficult to scale as it relies on RAM for computations. It supports thousands of nodes in a cluster. 
Language support	It uses Java or Python for MapReduce apps.	It uses Java, R, Scala, Python, or Spark SQL for the APIs.
User-friendliness	It is more difficult to use. 	It is more user-friendly.
Resource Management	YARN is the most common option for resource management.	It has built-in tools for resource management.

# Columnar Database

A columnar database, also known as a column-oriented database 
▪ A DBMS that stores data in columns rather than in rows as RDBMS
▪ The table schema defines only column families, which are the key value pairs. 
▪ A table can have multiple column families and each column family can have any number of 
columns.
▪ Subsequent column values are stored contiguously on the disk. 
▪ Each cell value of the table has a timestamp. 
▪ The tables in Columnar database are sorted by row.
▪ The rows of tables in Columnar database are identified by a row-key.
The difference centered around performance, storage and schema modifying techniques.
▪ Efficient write & read data to and from hard disk storage; speeds up the time it takes to return a 
query.
▪ Main benefits columnar operations like MIN, MAX, SUM, COUNT and AVG performed very 
rapidly.
▪ Columnar Database allows random read & write in Hadoop.
▪ Columnar database fulfills the ACID principles of a database. atomicity, consistency, isolation, and durability

# PIG Architecture

PIG Architecture

PIG provides an engine for executing data flows in parallel on Hadoop.
PIG Latin is a language used to analyse data in Hadoop.

APACHE PIG converts the scripts into MapReduce Jobs.

Components:

1.	Parser: PIG scripts are initially handled by the parser. It performs syntax and other miscellaneous checks of the script. The output is a DAG (Directed Acyclic Graph) file. 
2.	Optimizer: The DAG is passed to the optimizer. Here, optimization activities are performed such as split, merge, projection, transform, reorder etc. It improves the query performance by omitting any unnecessary data or columns through pushdown and projection activities
3.	Compiler: Here, the output from the optimizer is compiled into a series of Map Reduce jobs and rearranges their order to optimize the performance.
4.	Execution Engine: The Map Reduce jobs are then provided to Hadoop platform to produce the desired result.


LOAD DATA inpath 'hdfs:///myproject/output' OVERWRITE INTO TABLE insurance;
