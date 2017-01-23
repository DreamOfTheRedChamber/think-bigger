# Data processing pipeline

<!-- MarkdownTOC -->

- [Tell me about this project](#tell-me-about-this-project)
	- [Key points](#key-points)
	- [Describe](#describe)
- [Why did you use Apache Cassandra](#why-did-you-use-apache-cassandra)
	- [Data model](#data-model)
	- [Features](#features)
	- [Read/Write process](#readwrite-process)
- [Why did you use Apache Kafka](#why-did-you-use-apache-kafka)
	- [Advantages](#advantages)
	- [Comparison](#comparison)
- [Why did you use Apache Spark](#why-did-you-use-apache-spark)
- [What can be improved for the project](#what-can-be-improved-for-the-project)
- [Redis](#redis)

<!-- /MarkdownTOC -->

## Tell me about this project
### Key points
* Data types
* Describe project metrics
* Mention some new tech words

### Describe
* For this project I developed big data platform to process stock data in real-time. Stock data as we all know is time series data so I grabbed the data from Yahoo Finance. Then every record is about 200 bytes so for every record has last trading time for a stock and the last trading price, the last trading currency and the stock symbol, for example Apple. Because it is time-series data I need a way to quickly consume the data and put it into my system really fast. That's why I choose Kafka which is a high-throughput messaging system. I was able to achieve 200K message per second. If you count that message by multiplying the size of the data, it is about two terabytes data per day. So the normal storage system wouldn't fit and that's why I choose Apache Cassandra, which is a highly scalable peer-to-peer data storage system. The reason I choose it is also because Cassandra is peer-to-peer and there is no single-point of failure. Every node can go and I can just bring up a new one without hassle. Thirdly, I need a way to process data in realtime and that's why I choose Apache Spark. I use Spark streaming API and write a simple algorithm to process data in realtime. All those help me to process and store the data. I designed a simple web app using Node.js and Socket.io to render the process results in real-time. I use socket.IO to keep a web socket connection between client and server so that I could get the server to push data to client in real-time. For all the components I containerize them using Docker and Docker compose. 

## Why did you use Apache Cassandra
* Cassandra is a data store that was originally built at Facebook and could be seen as a merger of design patterns borrowed from BigTable and Dynamo. Cassandra is one of the clear leaders when it comes to ease of management, scalability, and self-healing, but it is important to remember that everything has its price. The main challenges that come with operating Cassandra are that it is heavily specialized, and it has a very particular data model, and it is an eventually consistent data store. 
* You can work around eventual conisstency by using quorum reads and writes, but the data model and tradeoffs made by the designers can often come as a surprise. Anything that you might have learned about relational databases is pretty much invalid when you work with NoSQL data stores like Cassandra. It is easy to get started with most NoSQL data stores, but to be able to operate them at scale takes much more experience and understanding of their internal structure than you might expect. 
	- For example, even though you can read in the open-source community that "Cassandra loves writes", deletes are the most expensive type of operation you can perform in Cassandra, which can come as a big suprise. Most people would not expect that deletes would be expensive type of operation you can perform in Cassandra. Cassandra uses append-only data structures, which allows it to write inserts with astonishing efficiency. Data is never overwritten in place and hard disks never have to perform random write operations, greatly increasing write throughput. But that feature, together with the fact that Cassandra is an eventually consistent datastore , forces deletes and updates to be internally persisted as inserts as well. As a result, some use cases that add and delete a lot of data can become inefficient because deletes increase the data set size rather than reducing it ( until the compaction process cleans them up ). 
	- A great example of how that can come as a surprise is a common Cassandra anti-pattern of a queue. You could model a simple first-in-first-out queue in Cassandra by using its dynamic columns. You add new entries to the queue by appending new columns, and you remove jobs from the queue by deleting columns. With a small scale and low volume of writes, this solution seems to work perfectly, but as you keep adding and deleting columns, your performance will begin to degrade dramatically. Although both inserts and deletes are perfectly fine and Cassandra purges old deleted data using its background compaction mechanism, it does not particularly like workloads with a such high rate of deletes (in this case, 50 percent of the operations are deletes).

### Data model 
* Level1: row_key
	- Namely hashkey
	- Could not perform range query
* Level2: column_key
	- Already sorted, could perform range query
	- Could be compound value (e.g. timestamp + user_id)
* Level3: value
	- In general it is String
	- Could use custom serialization or avaible ones such as Protobuff/Thrift.

### Features 
* Horizontal scalability / No single point of failure (peer to peer)
	- The more servers you add, the more read and write capacity you get. And you can easily scale in and out depending on your needs. In addition, since all of the topology is hidden from the clients, Cassandra is free to move data around. As a result, adding new servers is as easy as starting up a new node and telling it to join the cluster. 
	- All of its nodes perform the exact same functions. Nodes are functionally equal but each node is responsible for different data set. 
		+ Does not have a single point of failure.
		+ Automatic data partitioning.

* Write optimization: When a write is received by Cassandra, the data is first recorded in a commit log (append-only log), then written to an in-memory structure known as memtable. A write operation is only considered successful once it's written to the commit log and the memtable. Writes are batched in memory and periodically written out to structures know as SSTable. SSTable are not written to again after they are flushed; if there are changes to the data, a new SSTable is written. Unused SSTables are reclaimed by compaction.
	- Commit log: 
		+ Write to it before into db
	- Memtable:
		+ In memory
		+ Value will be added to memtable after commit log
	- SSTable
		+ Write after memtable is full
		+ Append only / Sequential write
	- Compaction 

* Tunable consistency
	- Consistency can be increased by reducing the availability level of request. 
	- Formula: R + W > N. Tune the availability by changing the R and W values for a fixed value of N.
		+ R: Minimum number of nodes that must respond successfully to a read
		+ W: Minimum number of nodes that must respond successfully to a write
		+ N: The number of nodes participating in the replication of data. Configured during keyspace creation.
	- Levels:
		+ ONE
			* Write: Write to one node's commit log and return a response to the client. Good when have high write requirements and do not mind if some writes are lost.
			* READ: Return data from the first replica. If the data is stale, subsequent reads will get the latest data; this process is called read repair. Good when you have high read requirements and do not mind if you get stale data.
		+ QUORUM: Similar to one, but the majority of the nodes need to respond to read/write requests.
	    + ALL: All nodes have to respond to reads or writes, which will make the cluster not tolerant to faults - even when one node is down, the write or read is blocked and reported as a failure. 

* Highly automated and little administration 
	- Replacing a failed node does not require complex backup recovery and replication offset tweaking, as often happens in MySQL. All you need to do to replace a broken server is add a new one and tell Cassandra which IP address this new node is replacing. Since each piece of data is stored on multiple servers, the cluster is fully operational throughout the server replacement procedure. Clients can read and write any data they wish even when one server is broken or being replaced. 

### Read/Write process  
* Clients can connect to any server no matter what data they intend to read or write. 
* Clients then issue queries to the coordinator node they chose without any knowledge about the topology or state of the cluster.
* Each of the Cassandra nodes knows the status of all other nodes and what data they are responsible for. They can delegate queries to the correct servers.	

## Why did you use Apache Kafka 
### Advantages
* Kafka is a high throughput message broker.
* First, Apache Kafka has high IO performance. First, audit files are written as sequential logs. Logs are written in an append-only manner to the disk. In addition, Kafka employs zero-copy API so all the data doesn't need to be copied up and down from user space to kernel spaces. 
* Second, Apache Kafka gives higher durability guarantee. Apache Kafka is based on Pull model and has an abstract concepts called offset. You can read the data and if you fail in the middle, you can simply retry. In addition, how Kafka deals with message consumers is different. Messages are not removed from the system. Instead, they rely on consumers to keep track of the last message consumed.

### Comparison
* Compared with traditional message brokers such as ActiveMQ/RabbitMQ, it has fewer constraints on explicit message ordering to improve throughput. 
* Compared with distributed data collection system like Flume, Kafka is a more generic message broker and Flume is a more complete Hadoop ingestion solution. Flume has good supports for writing data to Hadoop such as HDFS, HBase and Solr. If the requirements involve fault-tolerant message delivery, message reply and a large number of consumers, you should consider Kafka.

## Why did you use Apache Spark 
* Spark is a cluster computing system.
* Spark is typically much faster than MapReduce due to in-memory processing, Especially for iterative algorithms. In more detail, MapReduce does not leverage the memory of Hadoop cluster to the maximum, spark has the concept of RDD and caching which lets you save data or partial results in memory.
* With Spark it is possible to perform batch processing, streaming, interactive analysis altogether while MapReduce is only suitable for batch processing.

## What can be improved for the project
* Data visualization
* Data analysis
* Performance
* Infrastructure
	- Optimize payload size using Google Protocol Buffer to improve system throughput by 30%.
	- Use code profiling tools to identify which 
	- Use Apache Bench to benchmark the performance of the web app. 
	- Use Python concurrency
	- Use Pykka (Python Akka implementation)

## Redis 
* Redis is an in-memory key-value store.
* Compared with other key-value stores, Redis is much more faster. Redis can only serve data that fits in main memory. Although Redis has some replication features, it does not support features like eventual consistency. Even though Redis has been in the works for sometime, sharding and consistent hashing are provided by external services.
* Compared with other caching systems, Redis supports high-level data structures with atomic update capability. It also includes the capability to expire keys and publish-subscribe mechanism which can be used as messaging bus.
