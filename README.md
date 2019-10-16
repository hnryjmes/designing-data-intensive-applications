# Designing Data-Intensive Applications

### by Martin Kleppmann

`Notes by Henry Cooksley`

### Preface

"We call an application data-intensive if data is its primary challenge – the quantity of data, the complexity of data, or the speed at which it is changing – as opposed to compute-intensive, where CPU cycles are the bottleneck."

Who Should Read This Book?

"It is especially relevant if you need to make decisions about the architecture of the systems you work on – for example, if you need to choose tools for solving a given problem and figure out how best to apply them."

Scope of This Book

"This book has a bias towards free and open source software (FOSS), because reading, modifying, and executing source code is a great way to understand how something works in detail."

Outline of This Book

"In Part I, we discuss the fundamental ideas that underpin the design of data-intensive application."

"In Part II, we move from data stored on one machine to data that is distributed across multiple machines."

"In Part III, we discuss systems that derive some datasets from other datasets."

## I Foundations of Data Systems

### 1 Reliable, Scalable, and Maintainable Applications

Thinking About Data Systems

"Although a database and a message queue have some superficial similarity – both store data for some time – they have very different access patterns, which means different performance characteristics, and thus very different implementations."

"In the interest of thoughtful engineering, we will spend the rest of this chapter exploring ways of thinking about reliability, scalability, and maintainability."

Reliability

"A fault is usually defined as one component of the system deviating from its spec, whereas a failure is when the system as a whole stops providing the required service to the user."

Hardware Faults

"Until recently, redundancy of hardware components was sufficient for most applications, since it makes total failure of a single machine fairly rare."

Software Errors

"Lots of small things can help: carefully thinking about assumptions and interactions in the system; thorough testing; process isolation; allowing processes to crash and restart; measuring, monitoring, and analyzing system behavior in production."

Human Errors

"In particular, provide fully featured non-production sandbox environments where people can explore and experiment safely, using real data, without affecting real users."

How Important Is Reliability?

"Even in “noncritical” applications we have a responsibility to our users."

Scalability

"Scalability is the term we use to describe a system’s ability to cope with increased load."

Describing Load

"The best choice of parameters depends on the architecture of your system: it may be requests per second to a web server, the ratio of reads to writes in a database, the number of simultaneously active users in a chat room, the hit rate on a cache, or something else."

"This works better because the average rate of published tweets is almost two orders of magnitude lower than the rate of home timeline reads, and so in this case it’s preferable to do more work at write time and less at read time."

"This means that a single tweet may result in over 30 million writes to home timelines!"

"Doing this in a timely manner – Twitter tries to deliver tweets to followers within five seconds—is a significant challenge."

"The final twist of the Twitter anecdote: now that approach 2 is robustly implemented, Twitter is moving to a hybrid of both approaches."

Describing Performance

"When you increase a load parameter and keep the system resources (CPU, mem‐ ory, network bandwidth, etc.) unchanged, how is the performance of your system affected?"

"When you increase a load parameter, how much do you need to increase the resources if you want to keep performance unchanged?"

"In a batch processing system such as Hadoop, we usually care about throughput – the number of records we can process per second, or the total time it takes to run a job on a dataset of a certain size."

"Latency and response time are often used synonymously, but they are not the same."

"The response time is what the client sees: besides the actual time to process the request (the service time), it includes network delays and queueing delays."

"Latency is the duration that a request is waiting to be handled — during which it is latent, awaiting service."

"This makes the median a good metric if you want to know how long users typically have to wait: half of user requests are served in less than the median response time, and the other half take longer than the median."

"High percentiles of response times, also known as tail latencies, are important because they directly affect users’ experience of the service."

Percentiles in Practice

"Even if only a small percentage of backend calls are slow, the chance of getting a slow call increases if an end-user request requires multiple back‐ end calls, and so a higher proportion of end-user requests end up being slow (an effect known as tail latency amplification)."

"Beware that averaging percentiles, e.g., to reduce the time resolution or to com‐ bine data from several machines, is mathematically meaningless—the right way of aggregating response time data is to add the histograms."

Approaches for Coping with Load

"People often talk of a dichotomy between scaling up (vertical scaling, moving to a more powerful machine) and scaling out (horizontal scaling, distributing the load across multiple smaller machines)."

"While distributing stateless services across multiple machines is fairly straightforward, taking stateful data systems from a single node to a distributed setup can introduce a lot of additional complexity."

"For this reason, common wisdom until recently was to keep your database on a single node (scale up) until scaling cost or high-availability requirements forced you to make it distributed."

"The architecture of systems that operate at large scale is usually highly specific to the application—there is no such thing as a generic, one-size-fits-all scalable architecture (informally known as magic scaling sauce)."

Maintainability

"Every legacy system is unpleasant in its own way, and so it is difficult to give general recommendations for dealing with them."

"Rather, we will try to think about systems with operability, simplicity, and evolvability in mind."

Operability: Making Life Easy for Operations

"Good operability means making routine tasks easy, allowing the operations team to focus their efforts on high-value activities."

Simplicity: Managing Complexity

"There are various possible symptoms of complexity: explosion of the state space, tight coupling of modules, tangled dependencies, inconsistent naming and terminology, hacks aimed at solving performance problems, special-casing to work around issues elsewhere, and many more."

"One of the best tools we have for removing accidental complexity is abstraction."

"For example, high-level programming languages are abstractions that hide machine code, CPU registers, and syscalls."

"SQL is an abstraction that hides complex on-disk and in-memory data structures, concurrent requests from other clients, and inconsistencies after crashes."

"In the field of distributed systems, although there are many good algorithms, it is much less clear how we should be packaging them into abstractions that help us keep the complexity of the system at a manageable level."

Evolvability: Making Change Easy

"The Agile community has also developed technical tools and patterns that are helpful when developing software in a frequently changing environment, such as test-driven development (TDD) and refactoring."

Summary

"An application has to meet various requirements in order to be useful."

"There are functional requirements (what it should do, such as allowing data to be stored, retrieved, searched, and processed in various ways), and some nonfunctional requirements (general properties like security, reliability, compliance, scalability, compatibility, and maintainability)."

"Reliability means making systems work correctly, even when faults occur."

"Scalability means having strategies for keeping performance good, even when load increases."

"Maintainability has many facets, but in essence it’s about making life better for the engineering and operations teams who need to work with the system."

### 2 Data Models and Query Languages

"Data models are perhaps the most important part of developing software, because they have such a profound effect: not only on how the software is written, but also on how we think about the problem that we are solving."

Relational Model Versus Document Model

"The best-known data model today is probably that of SQL, based on the relational model proposed by Edgar Codd in 1970: data is organized into relations (called tables in SQL), where each relation is an unordered collection of tuples (rows in SQL)."

"The dominance of relational databases has lasted around 25‒30 years — an eternity in computing history."

"The use cases appear mundane from today’s perspective: typically transaction processing (entering sales or banking trans‐ actions, airline reservations, stock-keeping in warehouses) and batch processing (customer invoicing, payroll, reporting)."

The Birth of NoSQL

"Frustration with the restrictiveness of relational schemas, and a desire for a more dynamic and expressive data model"

The Object-Relational Mismatch

"Most application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows, and columns."

"Some developers feel that the JSON model reduces the impedance mismatch between the application code and the storage layer."

"If you want to fetch a profile in the relational example, you need to either perform multiple queries (query each table by `user_id`) or perform a messy multi-way join between the users table and its subordinate tables."

Many-to-One and Many-to-Many Relationships

"When you use an ID, the information that is meaningful to humans (such as the word Philanthropy) is stored in only one place, and everything that refers to it uses an ID (which only has meaning within the database)."

"In relational databases, it’s normal to refer to rows in other tables by ID, because joins are easy."

"In document databases, joins are not needed for one-to-many tree structures, and support for joins is often weak."

Are Document Databases Repeating History?

"This debate is much older than NoSQL — in fact, it goes back to the very earliest computerized database systems."

The network model

"In the tree structure of the hierarchical model, every record has exactly one parent; in the network model, a record could have multiple parents."

"The only way of accessing a record was to follow a path from a root record along these chains of links."

The relational model

"What the relational model did, by contrast, was to lay out all the data in the open: a relation (table) is simply a collection of tuples (rows), and that’s it."

"But a key insight of the relational model was this: you only need to build a query optimizer once, and then all applications that use the database can benefit from it."

Comparison to document databases

"However, when it comes to representing many-to-one and many-to-many relation‐ ships, relational and document databases are not fundamentally different: in both cases, the related item is referenced by a unique identifier, which is called a foreign key in the relational model and a document reference in the document model."

Relational Versus Document Databases Today

"The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by the application."

"The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships."

Which data model leads to simpler application code?

"It’s not possible to say in general which data model leads to simpler application code; it depends on the kinds of relationships that exist between data items."

Schema flexibility in the document model

"No schema means that arbitrary keys and values can be added to a document, and when reading, clients have no guarantees as to what fields the documents may contain."

Data locality for queries

"It’s worth pointing out that the idea of grouping related data together for locality is not limited to the document model."

"For example, Google’s Spanner database offers the same locality properties in a relational data model, by allowing the schema to declare that a table’s rows should be interleaved (nested) within a parent table."

Convergence of document and relational databases

"PostgreSQL since version 9.3, MySQL since version 5.7, and IBM DB2 since ver‐ sion 10.5 also have a similar level of support for JSON documents."

"If a database is able to handle document-like data and also perform relational queries on it, applications can use the combination of features that best fits their needs."

Query Languages for Data

"In the relational algebra, you would instead write: `sharks = σfamily = “Sharks” (animals)`"

"When SQL was defined, it followed the structure of the relational algebra fairly closely: `SELECT * FROM animals WHERE family = 'Sharks';`"

"The fact that SQL is more limited in functionality gives the database much more room for automatic optimizations."

"Declarative languages have a better chance of getting faster in parallel execution because they specify only the pattern of the results, not the algorithm that is used to determine the results."

Declarative Queries on the Web

"In a web browser, using declarative CSS styling is much better than manipulating styles imperatively in JavaScript."

"Similarly, in databases, declarative query languages like SQL turned out to be much better than imperative query APIs."

MapReduce Querying

"MapReduce is a programming model for processing large amounts of data in bulk across many machines, popularized by Google."

"A limited form of MapReduce is supported by some NoSQL datastores, including MongoDB and CouchDB, as a mechanism for performing read-only queries across many documents."

"To give an example, imagine you are a marine biologist, and you add an observation record to your database every time you see animals in the ocean."

"Now you want to generate a report saying how many sharks you have sighted per month."

"In PostgreSQL you might express that query like this:"

```
SELECT date_trunc('month', observation_timestamp) AS observation_month, sum(num_animals) AS total_animals 
FROM observations 
WHERE family = 'Sharks' 
GROUP BY observation_month;
```

"This query first filters the observations to only show species in the Sharks family, then groups the observations by the calendar month in which they occurred, and finally adds up the number of animals seen in all observations in that month."

"The same can be expressed with MongoDB’s MapReduce feature as follows:"

```
db.observations.mapReduce( function map() {
var year = this.observationTimestamp.getFullYear(); var month = this.observationTimestamp.getMonth() + 1; emit(year + "-" + month, this.numAnimals);
},
function reduce(key, values) {
return Array.sum(values); },
        {
            query: { family: "Sharks" },
            out: "monthlySharkReport"
} );
```

"The map and reduce functions are somewhat restricted in what they are allowed to do."

"They must be pure functions, which means they only use the data that is passed to them as input, they cannot perform additional database queries, and they must not have any side effects."

"The aggregation pipeline language is similar in expressiveness to a subset of SQL, but it uses a JSON-based syntax rather than SQL’s English-sentence-style syntax; the difference is perhaps a matter of taste."

Graph-Like Data Models

"The relational model can handle simple cases of many-to-many relationships, but as the connections within your data become more complex, it becomes more natural to start modeling your data as a graph."

"Well-known algorithms can operate on these graphs: for example, car navigation systems search for the shortest path between two points in a road network, and PageRank can be used on the web graph to determine the popularity of a web page and thus its ranking in search results."

Property Graphs

"Graphs are good for evolvability: as you add features to your application, a graph can easily be extended to accommodate changes in your application’s data structures."

The Cypher Query Language

"As is typical for a declarative query language, you don’t need to specify such execution details when writing the query: the query optimizer automatically chooses the strategy that is predicted to be the most efficient, so you can get on with writing the rest of your application."

Graph Queries in SQL

"In a graph query, you may need to traverse a variable number of edges before you find the vertex you’re looking for — that is, the number of joins is not fixed in advance."

Triple-Stores and SPARQL

"The triple-store model is mostly equivalent to the property graph model, using different words to describe the same ideas."

The semantic web

"The semantic web is fundamentally a simple and reasonable idea: websites already publish information as text and pictures for humans to read, so why don’t they also publish information as machine-readable data for computers to read?"

The RDF data model

"RDF has a few quirks due to the fact that it is designed for internet-wide data exchange."

The SPARQL query language

"SPARQL is a nice query language — even if the semantic web never happens, it can be a powerful tool for applications to use internally."

Graph Databases Compared to the Network Model

"Are graph databases the second coming of CODASYL in disguise?"

"In CODASYL, a database had a schema that specified which record type could be nested within which other record type."

"In a graph database, there is no such restriction: any vertex can have an edge to any other vertex."

The Foundation: Datalog

"Datalog’s data model is similar to the triple-store model, generalized a bit."

"Instead of writing a triple as (subject, predicate, object), we write it as predicate(subject, object)."

Summary

"Historically, data started out being represented as one big tree (the hierarchical model), but that wasn’t good for representing many-to-many relationships, so the relational model was invented to solve that problem."

"Document databases target use cases where data comes in self-contained documents and relationships between one document and another are rare."

"Graph databases go in the opposite direction, targeting use cases where anything is potentially related to everything."

"All three models (document, relational, and graph) are widely used today, and each is good in its respective domain."

### 3 Storage and Retrieval

"On the most fundamental level, a database needs to do two things: when you give it some data, it should store the data, and when you ask it again later, it should give the data back to you."

"You’re probably not going to implement your own storage engine from scratch, but you do need to select a storage engine that is appropriate for your application, from the many that are available."

"We will examine two families of storage engines: log-structured storage engines, and page-oriented storage engines such as B-trees."

Data Structures That Power Your Database

"Consider the world’s simplest database, implemented as two Bash functions:"

```bash
#!/bin/bash
    db_set () {
        echo "$1,$2" >> database
}
    db_get () {
        grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
}
```

"These two functions implement a key-value store."

"The underlying storage format is very simple: a text file where each line contains a key-value pair, separated by a comma (roughly like a CSV file, ignoring escaping issues)."

"Our `db_set` function actually has pretty good performance for something that is so simple, because appending to a file is generally very efficient."

"Similarly to what `db_set` does, many databases internally use a log, which is an append-only data file."

"Real databases have more issues to deal with (such as concurrency control, reclaiming disk space so that the log doesn’t grow forever, and handling errors and partially written records), but the basic principle is the same."

"In algorithmic terms, the cost of a lookup is O(n): if you double the number of records n in your database, a lookup takes twice as long."

"An index is an additional structure that is derived from the primary data."

Hash Indexes

"Key-value stores are quite similar to the dictionary type that you can find in most programming languages, and which is usually implemented as a hash map (hash table)."

"Whenever you append a new key-value pair to the file, you also update the hash map to reflect the offset of the data you just wrote (this works both for inserting new keys and for updating existing keys)."

"When you want to look up a value, use the hash map to find the offset in the data file, seek to that location, and read the value."

"As described so far, we only ever append to a file — so how do we avoid eventually running out of disk space?"

"A good solution is to break the log into segments of a certain size by closing a segment file when it reaches a certain size, and making subsequent writes to a new segment file."

"Segments are never modified after they have been written, so the merged segment is written to a new file."

"The merging and compaction of frozen segments can be done in a background thread, and while it is going on, we can still continue to serve read and write requests as normal, using the old segment files."

"Lots of detail goes into making this simple idea work in practice."

File format

"It’s faster and simpler to use a binary format that first encodes the length of a string in bytes, followed by the raw string (without need for escaping)."

Deleting records

"When log segments are merged, the tombstone tells the merging process to discard any previous values for the deleted key."

Crash recovery

"In principle, you can restore each segment’s hash map by reading the entire segment file from beginning to end and noting the offset of the most recent value for every key as you go along."

Partially written records

"The database may crash at any time, including halfway through appending a record to the log."

Concurrency control

"Data file segments are append-only and otherwise immutable, so they can be read concurrently by multiple threads."

"An append-only log seems wasteful at first glance: why don’t you update the file in place, overwriting the old value with the new value?"

"Appending and segment merging are sequential write operations, which are generally much faster than random writes, especially on magnetic spinning-disk hard drives."

"Concurrency and crash recovery are much simpler if segment files are append-only or immutable."

"Merging old segments avoids the problem of data files getting fragmented over time."

"The hash table must fit in memory, so if you have a very large number of keys, you’re out of luck."

SSTables and LSM-Trees

"Now we can make a simple change to the format of our segment files: we require that the sequence of key-value pairs is sorted by key."

"We call this format Sorted String Table, or SSTable for short."

"Merging segments is simple and efficient, even if the files are bigger than the available memory."

"In order to find a particular key in the file, you no longer need to keep an index of all the keys in memory."

"However, you do know the offsets for the keys handbag and handsome, and because of the sorting you know that handiwork must appear between those two."

"This means you can jump to the offset for handbag and scan from there until you find handiwork (or not, if the key is not present in the file)."

"You still need an in-memory index to tell you the offsets for some of the keys, but it can be sparse: one key for every few kilobytes of segment file is sufficient, because a few kilobytes can be scanned very quickly."

Constructing and maintaining SSTables

"It only suffers from one problem: if the database crashes, the most recent writes (which are in the memtable but not yet written out to disk) are lost."

"In order to avoid that problem, we can keep a separate log on disk to which every write is immediately appended, just like in the previous section."

"That log is not in sorted order, but that doesn’t matter, because its only purpose is to restore the memtable after a crash."

"Every time the memtable is written out to an SSTable, the corresponding log can be discarded."

Making an LSM-tree out of SSTables

"Originally this indexing structure was described by Patrick O’Neil et al. under the name Log-Structured Merge-Tree (or LSM-Tree), building on earlier work on log-structured filesystems."

"Storage engines that are based on this principle of merging and compacting sorted files are often called LSM storage engines."

Performance optimizations

"A Bloom filter is a memory-efficient data structure for approximating the contents of a set."

B-Trees

"The log-structured indexes we saw earlier break the database down into variable-size segments, typically several megabytes or more in size, and always write a segment sequentially."

"By contrast, B-trees break the database down into fixed-size blocks or pages, traditionally 4 KB in size (sometimes bigger), and read or write one page at a time."

"This design corresponds more closely to the underlying hardware, as disks are also arranged in fixed-size blocks."

"The number of references to child pages in one page of the B-tree is called the branching factor."

"In practice, the branching factor depends on the amount of space required to store the page references and the range boundaries, but typically it is several hundred."

"This algorithm ensures that the tree remains balanced: a B-tree with n keys always has a depth of O(log n)."

Making B-trees reliable

"An additional complication of updating pages in place is that careful concurrency control is required if multiple threads are going to access the B-tree at the same time — otherwise a thread may see the tree in an inconsistent state."

"This is typically done by protecting the tree’s data structures with latches (lightweight locks)."

B-tree optimizations

"Many B- tree implementations therefore try to lay out the tree so that leaf pages appear in sequential order on disk."

Comparing B-Trees and LSM-Trees

"Even though B-tree implementations are generally more mature than LSM-tree implementations, LSM-trees are also interesting due to their performance characteristics."

Advantages of LSM-trees

"In write-heavy applications, the performance bottleneck might be the rate at which the database can write to disk."

"In this case, write amplification has a direct performance cost: the more that a storage engine writes to disk, the fewer writes per second it can handle within the available disk bandwidth."

Downsides of LSM-trees

"An advantage of B-trees is that each key exists in exactly one place in the index, whereas a log-structured storage engine may have multiple copies of the same key in different segments."

Other Indexing Structures

"A primary key uniquely identifies one row in a relational table, or one document in a document database, or one vertex in a graph database."

"In relational databases, you can create several secondary indexes on the same table using the CREATE INDEX command, and they are often crucial for performing joins efficiently."

Storing values within the index

"The key in an index is the thing that queries search for, but the value can be one of two things: it could be the actual row (document, vertex) in question, or it could be a reference to the row stored elsewhere."

"A compromise between a clustered index (storing all row data within the index) and a nonclustered index (storing only references to the data within the index) is known as a covering index or index with included columns, which stores some of a table’s columns within the index."

Multi-column indexes

"The most common type of multi-column index is called a concatenated index, which simply combines several fields into one key by appending one column to another (the index definition specifies in which order the fields are concatenated)."

"Multi-dimensional indexes are a more general way of querying several columns at once, which is particularly important for geospatial data."

Full-text search and fuzzy indexes

"What they don’t allow you to do is search for similar keys, such as misspelled words."

"For example, full-text search engines commonly allow a search for one word to be expanded to include synonyms of the word, to ignore grammatical variations of words, and to search for occurrences of words near each other in the same document, and support various other features that depend on linguistic analysis of the text."

Keeping everything in memory

"However, we tolerate this awkwardness because disks have two significant advantages: they are durable (their contents are not lost if the power is turned off), and they have a lower cost per gigabyte than RAM."

"Counterintuitively, the performance advantage of in-memory databases is not due to the fact that they don’t need to read from disk."

"Rather, they can be faster because they can avoid the overheads of encoding in-memory data structures in a form that can be written to disk."

Transaction Processing or Analytics?

"In the early days of business data processing, a write to the database typically corresponded to a commercial transaction taking place: making a sale, placing an order with a supplier, paying an employee’s salary, etc."

"A transaction needn’t necessarily have ACID (atomicity, consistency, isolation, and durability) properties."

"Transaction processing just means allowing clients to make low-latency reads and writes — as opposed to batch processing jobs, which only run periodically (for example, once per day)."

"Even though databases started being used for many different kinds of data — comments on blog posts, actions in a game, contacts in an address book, etc. — the basic access pattern remained similar to processing business transactions."

"Usually an analytic query needs to scan over a huge number of records, only reading a few columns per record, and calculates aggregate statistics (such as count, sum, or average) rather than returning the raw data to the user."

"At first, the same databases were used for both transaction processing and analytic queries."

"SQL turned out to be quite flexible in this regard: it works well for OLTP-type queries as well as OLAP-type queries."

"This separate database was called a data warehouse."

"Nevertheless, in the late 1980s and early 1990s, there was a trend for companies to stop using their OLTP systems for analytics purposes, and to run the analytics on a separate database instead."

Data Warehousing

"The data warehouse contains a read-only copy of the data in all the various OLTP systems in the company."

"Data is extracted from OLTP databases (using either a periodic data dump or a continuous stream of updates), transformed into an analysis-friendly schema, cleaned up, and then loaded into the data warehouse."

"This process of getting data into the warehouse is known as Extract–Transform–Load (ETL)"

"A big advantage of using a separate data warehouse, rather than querying OLTP systems directly for analytics, is that the data warehouse can be optimized for analytic access patterns."

"It turns out that the indexing algorithms discussed in the first half of this chapter work well for OLTP, but are not very good at answering analytic queries."

The divergence between OLTP databases and data warehouses

"Some databases, such as Microsoft SQL Server and SAP HANA, have support for transaction processing and data warehousing in the same product."

"Amazon RedShift is a hosted version of ParAccel."

"More recently, a plethora of open source SQL-on- Hadoop projects have emerged; they are young but aiming to compete with commercial data warehouse systems."

"These include Apache Hive, Spark SQL, Cloudera Impala, Facebook Presto, Apache Tajo, and Apache Drill."

"Some of them are based on ideas from Google’s Dremel."

Stars and Snowflakes: Schemas for Analytics

"Other columns in the fact table are foreign key references to other tables, called dimension tables."

"As each row in the fact table represents an event, the dimensions represent the who, what, where, when, how, and why of the event."

"A variation of this template is known as the snowflake schema, where dimensions are further broken down into subdimensions."

Column-Oriented Storage

"Although fact tables are often over 100 columns wide, a typical data warehouse query only accesses 4 or 5 of them at one time ("`SELECT *`" queries are rarely needed for analytics)."

"The idea behind column-oriented storage is simple: don’t store all the values from one row together, but store all the values from each column together instead."

Column Compression

"Bitmap indexes such as these are very well suited for the kinds of queries that are common in a data warehouse."

Column-oriented storage and column families

"Thus, the Bigtable model is still mostly row-oriented."

Memory bandwidth and vectorized processing

"Besides reducing the volume of data that needs to be loaded from disk, column- oriented storage layouts are also good for making efficient use of CPU cycles."

Sort Order in Column Storage

"But having the first few columns sorted is still a win overall."

Several different sort orders

"Data needs to be replicated to multiple machines anyway, so that you don’t lose data if one machine fails."

"Having multiple sort orders in a column-oriented store is a bit similar to having multiple secondary indexes in a row-oriented store."

Writing to Column-Oriented Storage

"These optimizations make sense in data warehouses, because most of the load consists of large read-only queries run by analysts."

Aggregation: Data Cubes and Materialized Views

"Not every data warehouse is necessarily a column store: traditional row-oriented databases and a few other architectures are also used."

"The advantage of a materialized data cube is that certain queries become very fast because they have effectively been precomputed."

Summary

"On a high level, we saw that storage engines fall into two broad categories: those optimized for transaction processing (OLTP), and those optimized for analytics (OLAP)."

"OLTP systems are typically user-facing, which means that they may see a huge volume of requests."

"Data warehouses and similar analytic systems are less well known, because they are primarily used by business analysts, not by end users."

"As an application developer, if you’re armed with this knowledge about the internals of storage engines, you are in a much better position to know which tool is best suited for your particular application."

"If you need to adjust a database’s tuning parameters, this understanding allows you to imagine what effect a higher or a lower value may have."

### 4 Encoding and Evolution

"In most cases, a change to an application’s features also requires a change to data that it stores: perhaps a new field or record type needs to be captured, or perhaps existing data needs to be presented in a new way."

"With server-side applications you may want to perform a rolling upgrade (also known as a staged rollout), deploying the new version to a few nodes at a time, checking whether the new version is running smoothly, and gradually working your way through all the nodes."

"With client-side applications you’re at the mercy of the user, who may not install the update for some time."

"We will then discuss how those formats are used for data storage and for communication: in web services, Representational State Transfer (REST), and remote procedure calls (RPC), as well as message-passing systems such as actors and message queues."

Formats for Encoding Data

"In memory, data is kept in objects, structs, lists, arrays, hash tables, trees, and so on. These data structures are optimized for efficient access and manipulation by the CPU (typically using pointers)."

"When you want to write data to a file or send it over the network, you have to encode it as some kind of self-contained sequence of bytes (for example, a JSON document)."

"The translation from the in-memory representation to a byte sequence is called encoding (also known as serialization or marshalling), and the reverse is called decoding (parsing, deserialization, unmarshalling)."

Language-Specific Formats

"In order to restore data in the same object types, the decoding process needs to be able to instantiate arbitrary classes."

"This is frequently a source of security problems: if an attacker can get your application to decode an arbitrary byte sequence, they can instantiate arbitrary classes, which in turn often allows them to do terrible things such as remotely executing arbitrary code."

"For these reasons it’s generally a bad idea to use your language’s built-in encoding for anything other than very transient purposes."

JSON, XML, and Binary Variants

"JSON’s popularity is mainly due to its built-in support in web browsers (by virtue of being a subset of JavaScript) and simplicity relative to XML."

"An example of numbers larger than 253 occurs on Twitter, which uses a 64-bit number to identify each tweet."

"Despite these flaws, JSON, XML, and CSV are good enough for many purposes."

Binary encoding

"This observation led to the development of a profusion of binary encodings for JSON (MessagePack, BSON, BJSON, UBJSON, BISON, and Smile, to name a few) and for XML (WBXML and Fast Infoset, for example)."

Thrift and Protocol Buffers

"Protocol Buffers was originally developed at Google, Thrift was originally developed at Facebook, and both were made open source in 2007–08."

Field tags and schema evolution

"How do Thrift and Protocol Buffers handle schema changes while keeping backward and forward compatibility?"

"As long as each field has a unique tag number, new code can always read old data, because the tag numbers still have the same meaning."

"The only detail is that if you add a new field, you cannot make it required."

"Therefore, to maintain backward compatibility, every field you add after the initial deployment of the schema must be optional or have a default value."

Datatypes and schema evolution

"A curious detail of Protocol Buffers is that it does not have a list or array datatype, but instead has a repeated marker for fields (which is a third option alongside required and optional)."

"New code reading old data sees a list with zero or one elements (depending on whether the field was present); old code reading new data sees only the last element of the list."

Avro

"Apache Avro is another binary encoding format that is interestingly different from Protocol Buffers and Thrift."

"It was started in 2009 as a subproject of Hadoop, as a result of Thrift not being a good fit for Hadoop’s use cases."

"If you examine the byte sequence, you can see that there is nothing to identify fields or their datatypes."

"This means that the binary data can only be decoded correctly if the code reading the data is using the exact same schema as the code that wrote the data."

The writer’s schema and the reader’s schema

"The key idea with Avro is that the writer’s schema and the reader’s schema don’t have to be the same—they only need to be compatible."

Schema evolution rules

"If you were to add a field that has no default value, new readers wouldn’t be able to read data written by old writers, so you would break backward compatibility."

"If you were to remove a field that has no default value, old readers wouldn’t be able to read data written by new writers, so you would break forward compatibility."

But what is the writer’s schema?

"A common use for Avro — especially in the context of Hadoop — is for storing a large file containing millions of records, all encoded with the same schema."

"The simplest solution is to include a version number at the beginning of every encoded record, and to keep a list of schema versions in your database."

Dynamically generated schemas

"The difference is that Avro is friendlier to dynamically generated schemas."

"For example, say you have a relational database whose contents you want to dump to a file, and you want to use a binary format to avoid the aforementioned problems with textual formats (JSON, CSV, SQL)."

"If you use Avro, you can fairly easily generate an Avro schema (in the JSON representation we saw earlier) from the relational schema and encode the database contents using that schema, dumping it all to an Avro object container file."

"By contrast, if you were using Thrift or Protocol Buffers for this purpose, the field tags would likely have to be assigned by hand: every time the database schema changes, an administrator would have to manually update the mapping from database column names to field tags."

Code generation and dynamically typed languages

"Thrift and Protocol Buffers rely on code generation: after a schema has been defined, you can generate code that implements this schema in a programming language of your choice."

"In dynamically typed programming languages such as JavaScript, Ruby, or Python, there is not much point in generating code, since there is no compile-time type checker to satisfy."

The Merits of Schemas

"As we saw, Protocol Buffers, Thrift, and Avro all use a schema to describe a binary encoding format."

"In summary, schema evolution allows the same kind of flexibility as schemaless/ schema-on-read JSON databases provide, while also providing better guarantees about your data and better tooling."

Modes of Dataflow

"Compatibility is a relationship between one process that encodes the data, and another process that decodes it."

Different values written at different times

"Rewriting (migrating) data into a new schema is certainly possible, but it’s an expensive thing to do on a large dataset, so most databases avoid it if possible."

Archival storage

"As the data dump is written in one go and is thereafter immutable, formats like Avro object container files are a good fit."

Dataflow Through Services: REST and RPC

"Moreover, a server can itself be a client to another service (for example, a typical web app server acts as client to a database)."

"This approach is often used to decompose a large application into smaller services by area of functionality, such that one service makes a request to another when it requires some functionality or data from that other service."

"This restriction provides a degree of encapsulation: services can impose fine-grained restrictions on what clients can and cannot do."

"A key design goal of a service-oriented/microservices architecture is to make the application easier to change and maintain by making services independently deployable and evolvable."

Web services

"There are two popular approaches to web services: REST and SOAP."

"They are almost diametrically opposed in terms of philosophy, and often the subject of heated debate among their respective proponents."

"It emphasizes simple data formats, using URLs for identifying resources and using HTTP features for cache control, authentication, and content type negotiation."

"By contrast, SOAP is an XML-based protocol for making network API requests."

"The API of a SOAP web service is described using an XML-based language called the Web Services Description Language, or WSDL."

"As WSDL is not designed to be human-readable, and as SOAP messages are often too complex to construct manually, users of SOAP rely heavily on tool support, code generation, and IDEs."

The problems with remote procedure calls (RPCs)

"The RPC model tries to make a request to a remote network service look the same as calling a function or method in your programming language, within the same process (this abstraction is called location transparency)."

"A local function call either returns a result, or throws an exception, or never returns (because it goes into an infinite loop or the process crashes)."

"A network request has another possible outcome: it may return without a result, due to a timeout."

"The client and the service may be implemented in different programming languages, so the RPC framework must translate datatypes from one language into another."

"All of these factors mean that there’s no point trying to make a remote service look too much like a local object in your programming language, because it’s a fundamentally different thing."

Current directions for RPC

"Various RPC frameworks have been built on top of all the encodings mentioned in this chapter: for example, Thrift and Avro come with RPC support included, gRPC is an RPC implementation using Protocol Buffers, Finagle also uses Thrift, and Rest.li uses JSON over HTTP."

"Custom RPC protocols with a binary encoding format can achieve better performance than something generic like JSON over REST."

Data encoding and evolution for RPC

"If a compatibility-breaking change is required, the service provider often ends up maintaining multiple versions of the service API side by side."

Message-Passing Dataflow

"In this final section, we will briefly look at asynchronous message-passing systems, which are somewhere between RPC and databases."

"They are similar to RPC in that a client’s request (usually called a message) is delivered to another process with low latency."

"They are similar to databases in that the message is not sent via a direct network connection, but goes via an intermediary called a message broker (also called a message queue or message-oriented middleware), which stores the message temporarily."

Message brokers

"The detailed delivery semantics vary by implementation and configuration, but in general, message brokers are used as follows: one process sends a message to a named queue or topic, and the broker ensures that the message is delivered to one or more consumers of or subscribers to that queue or topic."

"Message brokers typically don’t enforce any particular data model—a message is just a sequence of bytes with some metadata, so you can use any encoding format."

Distributed actor frameworks

"The actor model is a programming model for concurrency in a single process."

"Rather than dealing directly with threads (and the associated problems of race conditions, locking, and deadlock), logic is encapsulated in actors."

"In distributed actor frameworks, this programming model is used to scale an application across multiple nodes."

"A distributed actor framework essentially integrates a message broker and the actor programming model into a single framework."

"Akka uses Java’s built-in serialization by default, which does not provide forward or backward compatibility."

Summary

"We saw how the details of these encodings affect not only their efficiency, but more importantly also the architecture of applications and your options for deploying them."

"In particular, many services need to support rolling upgrades, where a new version of a service is gradually deployed to a few nodes at a time, rather than deploying to all nodes simultaneously."

"These properties are hugely beneficial for evolvability, the ease of making changes to an application."

"Programming language–specific encodings are restricted to a single programming language and often fail to provide forward and backward compatibility."

"Textual formats like JSON, XML, and CSV are widespread, and their compatibility depends on how you use them."

"Binary schema–driven formats like Thrift, Protocol Buffers, and Avro allow compact, efficient encoding with clearly defined forward and backward compatibility semantics."

"We can conclude that with a bit of care, backward/forward compatibility and rolling upgrades are quite achievable."

## II Distributed Data

Scalability

"If your data volume, read load, or write load grows bigger than a single machine can handle, you can potentially spread the load across multiple machines."

Fault tolerance/high availability

"If your application needs to continue working even if one machine (or several machines, or the network, or an entire datacenter) goes down, you can use multiple machines to give you redundancy."

Latency

"If you have users around the world, you might want to have servers at various locations worldwide so that each user can be served from a datacenter that is geographically close to them."

Scaling to Higher Load

"The problem with a shared-memory approach is that the cost grows faster than linearly: a machine with twice as many CPUs, twice as much RAM, and twice as much disk capacity as another typically costs significantly more than twice as much."

Shared-Nothing Architectures

"In this approach, each machine or virtual machine running the database software is called a node."

"You can potentially distribute data across multiple geographic regions, and thus reduce latency for users and potentially be able to survive the loss of an entire datacenter."

"In some cases, a simple single-threaded program can perform significantly better than a cluster with over 100 CPU cores."

Replication Versus Partitioning

Replication

"Replication provides redundancy: if some nodes are unavailable, the data can still be served from the remaining nodes."

Partitioning

"Splitting a big database into smaller subsets called partitions so that different partitions can be assigned to different nodes (also known as sharding)."

### 5 Replication

"If the data that you’re replicating does not change over time, then replication is easy: you just need to copy the data to every node once, and you’re done."

"We will discuss three popular algorithms for replicating changes between nodes: single-leader, multi-leader, and leaderless replication."

"There are many trade-offs to consider with replication: for example, whether to use synchronous or asynchronous replication, and how to handle failed replicas."

Leaders and Followers

"When clients want to write to the database, they must send their requests to the leader, which first writes the new data to its local storage."

"Whenever the leader writes new data to its local storage, it also sends the data change to all of its followers as part of a replication log or change stream."

Synchronous Versus Asynchronous Replication

"An important detail of a replicated system is whether the replication happens synchronously or asynchronously."

"Normally, replication is quite fast: most database systems apply changes to followers in less than a second."

"There are circumstances when followers might fall behind the leader by several minutes or more; for example, if a follower is recovering from a failure, if the system is operating near maximum capacity, or if there are network problems between the nodes."

"For that reason, it is impractical for all followers to be synchronous: any one node outage would cause the whole system to grind to a halt."

"However, a fully asynchronous configuration has the advantage that the leader can continue processing writes, even if all of its followers have fallen behind."

Research on Replication

"It can be a serious problem for asynchronously replicated systems to lose data if the leader fails, so researchers have continued investigating replication methods that do not lose data but still provide good performance and availability."

Setting Up New Followers

"In some systems the process is fully automated, whereas in others it can be a somewhat arcane multi-step workflow that needs to be manually performed by an administrator."

Handling Node Outages

"Thus, our goal is to keep the system as a whole running despite individual node failures, and to keep the impact of a node outage as small as possible."

Follower failure: Catch-up recovery

"If a follower crashes and is restarted, or if the network between the leader and the follower is temporarily interrupted, the follower can recover quite easily: from its log, it knows the last transaction that was processed before the fault occurred."

Leader failure: Failover

"Handling a failure of the leader is trickier: one of the followers needs to be promoted to be the new leader, clients need to be reconfigured to send their writes to the new leader, and the other followers need to start consuming data changes from the new leader."

"There is no foolproof way of detecting what has gone wrong, so most systems simply use a timeout: nodes frequently bounce messages back and forth between each other, and if a node doesn’t respond for some period of time — say, 30 seconds — it is assumed to be dead."

"This could be done through an election process (where the leader is chosen by a majority of the remaining replicas), or a new leader could be appointed by a previously elected controller node."

"The system needs to ensure that the old leader becomes a follower and recognizes the new leader."

"If asynchronous replication is used, the new leader may not have received all the writes from the old leader before it failed."

"Discarding writes is especially dangerous if other storage systems outside of the database need to be coordinated with the database contents."

"For example, in one incident at GitHub, an out-of-date MySQL follower was promoted to leader."

"The database used an autoincrementing counter to assign primary keys to new rows, but because the new leader’s counter lagged behind the old leader’s, it reused some primary keys that were previously assigned by the old leader."

"These primary keys were also used in a Redis store, so the reuse of primary keys resulted in inconsistency between MySQL and Redis, which caused some private data to be disclosed to the wrong users."

"This situation is called split brain, and it is dangerous: if both leaders accept writes, and there is no process for resolving conflicts, data is likely to be lost or corrupted."

"What is the right timeout before the leader is declared dead?"

"If the system is already struggling with high load or network problems, an unnecessary failover is likely to make the situation worse, not better."

Implementation of Replication Logs

Statement-based replication

"In the simplest case, the leader logs every write request (statement) that it executes and sends that statement log to its followers."

Write-ahead log (WAL) shipping

"We can use the exact same log to build a replica on another node: besides writing the log to disk, the leader also sends it across the network to its followers."

"The main disadvantage is that the log describes the data on a very low level: a WAL contains details of which bytes were changed in which disk blocks."

"If the replication protocol allows the follower to use a newer software version than the leader, you can perform a zero-downtime upgrade of the database software by first upgrading the followers and then performing a failover to make one of the upgraded nodes the new leader."

Logical (row-based) log replication

"An alternative is to use different log formats for replication and for the storage engine, which allows the replication log to be decoupled from the storage engine internals."

"Since a logical log is decoupled from the storage engine internals, it can more easily be kept backward compatible, allowing the leader and the follower to run different versions of the database software, or even different storage engines."

Trigger-based replication

"Trigger-based replication typically has greater overheads than other replication methods, and is more prone to bugs and limitations than the database’s built-in replication."

Problems with Replication Lag

"Leader-based replication requires all writes to go through a single node, but read-only queries can go to any replica."

"This leads to apparent inconsistencies in the database: if you run the same query on the leader and a follower at the same time, you may get different results, because not all writes have been reflected in the follower."

"However, if the system is operating near capacity or if there is a problem in the network, the lag can easily increase to several seconds or even minutes."

Reading Your Own Writes

"With asynchronous replication, there is a problem: if the user views the data shortly after making a write, the new data may not yet have reached the replica."

"To the user, it looks as though the data they submitted was lost, so they will be understandably unhappy."

"In this situation, we need read-after-write consistency, also known as read-your-writes consistency."

"Thus, a simple rule is: always read the user’s own profile from the leader, and any other users’ profiles from a follower."

"If your replicas are distributed across multiple datacenters (for geographical proximity to users or for availability), there is additional complexity."

"Another complication arises when the same user is accessing your service from multiple devices, for example a desktop web browser and a mobile app."

Monotonic Reads

"Our second example of an anomaly that can occur when reading from asynchronous followers is that it’s possible for a user to see things moving backward in time."

"When you read data, you may see an old value; monotonic reads only means that if one user makes several reads in sequence, they will not see time go backward — i.e., they will not read older data after having previously read newer data."

Consistent Prefix Reads

"Preventing this kind of anomaly requires another type of guarantee: consistent prefix reads."

"However, in many distributed databases, different partitions operate independently, so there is no global ordering of writes: when a user reads from the database, they may see some parts of the database in an older state and some in a newer state."

Solutions for Replication Lag

"Pretending that replication is synchronous when in fact it is asynchronous is a recipe for problems down the line."

"It would be better if application developers didn’t have to worry about subtle replication issues and could just trust their databases to “do the right thing.”"

"This is why transactions exist: they are a way for a database to provide stronger guarantees so that the application can be simpler."

Multi-Leader Replication

"Leader-based replication has one major downside: there is only one leader, and all writes must go through it."

"We call this a multi-leader configuration (also known as master–master or active/active replication)."

Use Cases for Multi-Leader Replication

Multi-datacenter operation

"In a multi-leader configuration, you can have a leader in each datacenter."

Performance

"In a multi-leader configuration, every write can be processed in the local datacenter and is replicated asynchronously to the other datacenters."

Tolerance of datacenter outages

"In a multi-leader configuration, each datacenter can continue operating independently of the others, and replication catches up when the failed datacenter comes back online."

Tolerance of network problems

"A multi-leader configuration with asynchronous replication can usually tolerate network problems better: a temporary network interruption does not prevent writes being processed."

Clients with offline operation

"You need to be able to see your meetings (make read requests) and enter new meetings (make write requests) at any time, regardless of whether your device currently has an internet connection."

"From an architectural point of view, this setup is essentially the same as multi-leader replication between datacenters, taken to the extreme: each device is a “datacenter,” and the network connection between them is extremely unreliable."

Collaborative editing

"We don’t usually think of collaborative editing as a database replication problem, but it has a lot in common with the previously mentioned offline editing use case."

"However, for faster collaboration, you may want to make the unit of change very small (e.g., a single keystroke) and avoid locking."

Handling Write Conflicts

"The biggest problem with multi-leader replication is that write conflicts can occur, which means that conflict resolution is required."

Synchronous versus asynchronous conflict detection

"If you want synchronous conflict detection, you might as well just use single-leader replication."

Conflict avoidance

"The simplest strategy for dealing with conflicts is to avoid them: if the application can ensure that all writes for a particular record go through the same leader, then conflicts cannot occur."

Converging toward a consistent state

"Give each write a unique ID (e.g., a timestamp, a long random number, a UUID, or a hash of the key and value), pick the write with the highest ID as the winner, and throw away the other writes."

Custom conflict resolution logic

On write

"As soon as the database system detects a conflict in the log of replicated changes, it calls the conflict handler."

On read

"The next time the data is read, these multiple versions of the data are returned to the application."

Automatic Conflict Resolution

"Amazon is a frequently cited example of surprising effects due to a conflict resolution handler: for some time, the conflict resolution logic on the shopping cart would preserve items added to the cart, but not items removed from the cart."

What is a conflict?

"This application needs to ensure that each room is only booked by one group of people at any one time (i.e., there must not be any overlapping bookings for the same room)."

Multi-Leader Replication Topologies

"A replication topology describes the communication paths along which writes are propagated from one node to another."

"The most general topology is all-to-all, in which every leader sends its writes to every other leader."

"However, more restricted topologies are also used: for example, MySQL by default supports only a circular topology, in which each node receives writes from one node and forwards those writes (plus any writes of its own) to one other node."

"Another popular topology has the shape of a star: one designated root node forwards writes to all of the other nodes."

Leaderless Replication

"Some data storage systems take a different approach, abandoning the concept of a leader and allowing any replica to directly accept writes from clients."

"Riak, Cassandra, and Voldemort are open source datastores with leaderless replication models inspired by Dynamo, so this kind of database is also known as Dynamo-style."

Writing to the Database When a Node Is Down

"To solve that problem, when a client reads from the database, it doesn’t just send its request to one replica: read requests are also sent to several nodes in parallel."

Read repair and anti-entropy

Read repair

"The client sees that replica 3 has a stale value and writes the newer value back to that replica."

Anti-entropy process

"In addition, some datastores have a background process that constantly looks for differences in the data between replicas and copies any missing data from one replica to another."

Quorums for reading and writing

"More generally, if there are n replicas, every write must be confirmed by w nodes to be considered successful, and we must query at least r nodes for each read."

"As long as w + r > n, we expect to get an up-to-date value when reading, because at least one of the r nodes we’re reading from must be up to date."

Limitations of Quorum Consistency

"With a smaller w and r you are more likely to read stale values, because it’s more likely that your read didn’t include the node with the latest value."

"Thus, although quorums appear to guarantee that a read returns the latest written value, in practice it is not so simple."

"Dynamo-style databases are generally optimized for use cases that can tolerate eventual consistency."

Monitoring staleness

"For leader-based replication, the database typically exposes metrics for the replication lag, which you can feed into a monitoring system."

Sloppy Quorums and Hinted Handoff

"In a large cluster (with significantly more than n nodes) it’s likely that the client can connect to some database nodes during the network interruption, just not to the nodes that it needs to assemble a quorum for a particular value."

"The latter is known as a sloppy quorum: writes and reads still require w and r successful responses, but those may include nodes that are not among the designated n “home” nodes for a value."

"By analogy, if you lock yourself out of your house, you may knock on the neighbor’s door and ask whether you may stay on their couch temporarily."

Multi-datacenter operation

"Leaderless replication is also suitable for multi-datacenter operation, since it is designed to tolerate conflicting concurrent writes, network interruptions, and latency spikes."

Detecting Concurrent Writes

"The problem is that events may arrive in a different order at different nodes, due to variable network delays and partial failures."

Last write wins (discarding concurrent writes)

"One approach for achieving eventual convergence is to declare that each replica need only store the most “recent” value and allow “older” values to be overwritten and discarded."

"If losing data is not acceptable, LWW is a poor choice for conflict resolution."

The “happens-before” relationship and concurrency

"An operation A happens before another operation B if B knows about A, or depends on A, or builds upon A in some way."

"Thus, whenever you have two operations A and B, there are three possibilities: either A happened before B, or B happened before A, or A and B are concurrent."

Concurrency, Time, and Relativity

"Consequently, two events that occur some distance apart cannot possibly affect each other if the time between the events is shorter than the time it takes light to travel the distance between them."

"In computer systems, two operations might be concurrent even though the speed of light would in principle have allowed one operation to affect the other."

Capturing the happens-before relationship

"Once we have worked out how to do this on a single replica, we can generalize the approach to a leaderless database with multiple replicas."

Merging concurrently written values

"To prevent this problem, an item cannot simply be deleted from the database when it is removed; instead, the system must leave a marker with an appropriate version number to indicate that the item has been removed when merging siblings."

Version vectors

"The version vector structure ensures that it is safe to read from one replica and subsequently write back to another replica."

Version vectors and vector clocks

"A version vector is sometimes also called a vector clock, even though they are not quite the same."

Summary

High availability

"Keeping the system running, even when one machine (or several machines, or an entire datacenter) goes down"

Disconnected operation

"Allowing an application to continue working when there is a network interruption"

Latency

"Placing data geographically close to users, so that users can interact with it faster"

Scalability

"Being able to handle a higher volume of reads than a single machine could handle, by performing reads on replicas"

"At a minimum, we need to deal with unavailable nodes and network interruptions (and that’s not even considering the more insidious kinds of fault, such as silent data corruption due to software bugs)."

Single-leader replication

"Clients send all writes to a single node (the leader), which sends a stream of data change events to the other replicas (followers)."

Multi-leader replication

"Clients send each write to one of several leader nodes, any of which can accept writes."

Leaderless replication

"Clients send each write to several nodes, and read from several nodes in parallel in order to detect and correct nodes with stale data."

Read-after-write consistency

"Users should always see data that they submitted themselves."

Monotonic reads

"After users have seen the data at one point in time, they shouldn’t later see the data from some earlier point in time."

Consistent prefix reads

"Users should see the data in a state that makes causal sense: for example, seeing a question and its reply in the correct order."

### 6 Partitioning

"For very large datasets, or very high query throughput, that is not sufficient: we need to break the data up into partitions, also known as sharding."

Terminological confusion

"What we call a partition here is called a shard in MongoDB, Elasticsearch, and SolrCloud; it’s known as a region in HBase, a tablet in Bigtable, a vnode in Cassandra and Riak, and a vBucket in Couchbase."

"The main reason for wanting to partition data is scalability."

Partitioning and Replication

"This means that, even though each record belongs to exactly one partition, it may still be stored on several different nodes for fault tolerance."

Partitioning of Key-Value Data

"If the partitioning is unfair, so that some partitions have more data or queries than others, we call it skewed."

"The simplest approach for avoiding hot spots would be to assign records to nodes randomly."

Partitioning by Key Range

"However, the downside of key range partitioning is that certain access patterns can lead to hot spots."

"To avoid this problem in the sensor database, you need to use something other than the timestamp as the first element of the key."

Partitioning by Hash of Key

"Because of this risk of skew and hot spots, many distributed datastores use a hash function to determine the partition for a given key."

"A good hash function takes skewed data and makes it uniformly distributed."

Consistent Hashing

"Because this is so confusing, it’s best to avoid the term consistent hashing and just call it hash partitioning instead."

"Unfortunately however, by using the hash of the key for partitioning we lose a nice property of key-range partitioning: the ability to do efficient range queries."

Skewed Workloads and Relieving Hot Spots

"Just a two-digit decimal random number would split the writes to the key evenly across 100 different keys, allowing those keys to be distributed to different partitions."

Partitioning and Secondary Indexes

"The problem with secondary indexes is that they don’t map neatly to partitions."

Partitioning Secondary Indexes by Document

"However, reading from a document-partitioned index requires care: unless you have done something special with the document IDs, there is no reason why all the cars with a particular color or a particular make would be in the same partition."

"Thus, if you want to search for red cars, you need to send the query to all partitions, and combine all the results you get back."

"This approach to querying a partitioned database is sometimes known as scatter/ gather, and it can make read queries on secondary indexes quite expensive."

Partitioning Secondary Indexes by Term

"We call this kind of index term-partitioned, because the term we’re looking for determines the partition of the index."

"The advantage of a global (term-partitioned) index over a document-partitioned index is that it can make reads more efficient: rather than doing scatter/gather over all partitions, a client only needs to make a request to the partition containing the term that it wants."

"However, the downside of a global index is that writes are slower and more complicated, because a write to a single document may now affect multiple partitions of the index (every term in the document might be on a different partition, on a different node)."

Rebalancing Partitions

"The process of moving load from one node in the cluster to another is called rebalancing."

Strategies for Rebalancing

How not to do it: hash mod N

"The problem with the mod N approach is that if the number of nodes N changes, most of the keys will need to be moved from one node to another."

Fixed number of partitions

"For example, a database running on a cluster of 10 nodes may be split into 1,000 partitions from the outset so that approximately 100 partitions are assigned to each node."

Dynamic partitioning

"When a partition grows to exceed a configured size (on HBase, the default is 10 GB), it is split into two partitions so that approximately half of the data ends up on each side of the split."

Partitioning proportionally to nodes

"When a new node joins the cluster, it randomly chooses a fixed number of existing partitions to split, and then takes ownership of one half of each of those split partitions while leaving the other half of each partition in place."

Operations: Automatic or Manual Rebalancing

"For example, say one node is overloaded and is temporarily slow to respond to requests."

"The other nodes conclude that the overloaded node is dead, and automatically rebalance the cluster to move load away from it."

"This puts additional load on the overloaded node, other nodes, and the network — making the situation worse and potentially causing a cascading failure."

Request Routing

"Somebody needs to stay on top of those changes in order to answer the question: if I want to read or write the key “foo”, which IP address and port number do I need to connect to?"

"Each node registers itself in ZooKeeper, and ZooKeeper maintains the authoritative mapping of partitions to nodes."

"Whenever a partition changes ownership, or a node is added or removed, ZooKeeper notifies the routing tier so that it can keep its routing information up to date."

"HBase, SolrCloud, and Kafka also use ZooKeeper to track partition assignment."

"Cassandra and Riak take a different approach: they use a gossip protocol among the nodes to disseminate any changes in cluster state."

Parallel Query Execution

"However, massively parallel processing (MPP) relational database products, often used for analytics, are much more sophisticated in the types of queries they support."

Summary

"Partitioning is necessary when you have so much data that storing and processing it on a single machine is no longer feasible."

"Key range partitioning, where keys are sorted, and a partition owns all the keys from some minimum up to some maximum."

"Hash partitioning, where a hash function is applied to each key, and a partition owns a range of hashes."

"Document-partitioned indexes (local indexes), where the secondary indexes are stored in the same partition as the primary key and value."

"Term-partitioned indexes (global indexes), where the secondary indexes are partitioned separately, using the indexed values."

### 7 Transactions

"The database software or hardware may fail at any time (including in the middle of a write operation)."

"The application may crash at any time (including halfway through a series of operations)."

"Interruptions in the network can unexpectedly cut off the application from the database, or one database node from another."

"Several clients may write to the database at the same time, overwriting each other’s changes."

"A client may read data that doesn’t make sense because it has only partially been updated."

"Race conditions between clients can cause surprising bugs."

"Conceptually, all the reads and writes in a transaction are executed as one operation: either the entire transaction succeeds (commit) or it fails (abort, rollback)."

"Transactions are not a law of nature; they were created with a purpose, namely to simplify the programming model for applications accessing a database."

"We will go especially deep in the area of concurrency control, discussing various kinds of race conditions that can occur and how databases implement isolation levels such as read committed, snapshot isolation, and serializability."

The Slippery Concept of a Transaction

"Transactions were the main casualty of this movement: many of this new generation of databases abandoned transactions entirely, or redefined the word to describe a much weaker set of guarantees than had previously been understood."

The Meaning of ACID

"The safety guarantees provided by transactions are often described by the well-known acronym ACID, which stands for Atomicity, Consistency, Isolation, and Durability."

"Today, when a system claims to be “ACID compliant,” it’s unclear what guarantees you can actually expect."

"Systems that do not meet the ACID criteria are sometimes called BASE, which stands for Basically Available, Soft state, and Eventual consistency."

Atomicity

"In general, atomic refers to something that cannot be broken down into smaller parts."

"For example, in multi-threaded programming, if one thread executes an atomic operation, that means there is no way that another thread could see the half-finished result of the operation."

"If the writes are grouped together into an atomic transaction, and the transaction cannot be completed (committed) due to a fault, then the transaction is aborted and the database must discard or undo any writes it has made so far in that transaction."

"Perhaps abortability would have been a better term than atomicity, but we will stick with atomicity since that’s the usual word."

Consistency

"In the context of ACID, consistency refers to an application-specific notion of the database being in a “good state.”"

"The idea of ACID consistency is that you have certain statements about your data (invariants) that must always be true — for example, in an accounting system, credits and debits across all accounts must always be balanced."

"Atomicity, isolation, and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application."

Isolation

"Isolation in the sense of ACID means that concurrently executing transactions are isolated from each other: they cannot step on each other’s toes."

"The classic database textbooks formalize isolation as serializability, which means that each transaction can pretend that it is the only transaction running on the entire database."

Durability

"Durability is the promise that once a transaction has committed successfully, any data it has written will not be forgotten, even if there is a hardware fault or the database crashes."

Replication and Durability

"If you write to disk and the machine dies, even though your data isn’t lost, it is inaccessible until you either fix the machine or transfer the disk to another machine."

"When the power is suddenly cut, SSDs in particular have been shown to sometimes violate the guarantees they are supposed to provide: even `fsync` isn’t guaranteed to work correctly."

"There are only various risk-reduction techniques, including writing to disk, replicating to remote machines, and backups — and they can and should be used together."

Single-Object and Multi-Object Operations

"In other words, the database saves you from having to worry about partial failure, by giving an all-or-nothing guarantee."

"For example, if one transaction makes several writes, then another transaction should see either all or none of those writes, but not some subset."

Single-object writes

"A transaction is usually understood as a mechanism for grouping multiple operations on multiple objects into one unit of execution."

The need for multi-object transactions

"In a relational data model, a row in one table often has a foreign key reference to a row in another table."

"Multi-object transactions allow you to ensure that these references remain valid: when inserting several records that refer to one another, the foreign keys have to be correct and up to date, or the data becomes nonsensical."

Handling errors and aborts

"For example, popular object-relational mapping (ORM) frameworks such as Rails’s ActiveRecord and Django don’t retry aborted transactions—the error usually results in an exception bubbling up the stack, so any user input is thrown away and the user gets an error message."

"If the transaction also has side effects outside of the database, those side effects may happen even if the transaction is aborted."

"For example, if you’re sending an email, you wouldn’t want to send the email again every time you retry the transaction."

Weak Isolation Levels

"Concurrency bugs are hard to find by testing, because such bugs are only triggered when you get unlucky with the timing."

"Serializable isolation has a performance cost, and many databases don’t want to pay that price."

"Concurrency bugs caused by weak transaction isolation are not just a theoretical problem."

"They have caused substantial loss of money, led to investigation by financial auditors, and caused customer data to be corrupted."

"A popular comment on revelations of such problems is “Use an ACID database if you’re handling financial data!”— but that misses the point."

"Even many popular relational database systems (which are usually considered “ACID”) use weak isolation, so they wouldn’t necessarily have prevented these bugs from occurring."

Read Committed

"When reading from the database, you will only see data that has been committed (no dirty reads)."

"When writing to the database, you will only overwrite data that has been committed (no dirty writes)."

No dirty reads

"This means that any writes by a transaction only become visible to others when that transaction commits (and then all of its writes become visible at once)."

No dirty writes

"Transactions running at the read committed isolation level must prevent dirty writes, usually by delaying the second write until the first write’s transaction has committed or aborted."

Implementing read committed

"Most commonly, databases prevent dirty writes by using row-level locks: when a transaction wants to modify a particular object (row or document), it must first acquire a lock on that object."

"However, the approach of requiring read locks does not work well in practice, because one long-running write transaction can force many read-only transactions to wait until the long-running transaction has completed."

Snapshot Isolation and Repeatable Read

"To Alice it now appears as though she only has a total of $900 in her accounts — it seems that $100 has vanished into thin air."

"This anomaly is called a nonrepeatable read or read skew: if Alice were to read the balance of account 1 again at the end of the transaction, she would see a different value ($600) than she saw in her previous query."

"The idea is that each transaction reads from a consistent snapshot of the database — that is, the transaction sees all the data that was committed in the database at the start of the transaction."

"Snapshot isolation is a boon for long-running, read-only queries such as backups and analytics."

Implementing snapshot isolation

"From a performance point of view, a key principle of snapshot isolation is readers never block writers, and writers never block readers."

"Because it maintains several versions of an object side by side, this technique is known as multi-version concurrency control (MVCC)."

Visibility rules for observing a consistent snapshot

"By never updating values in place but instead creating a new version every time a value is changed, the database can provide a consistent snapshot while incurring only a small overhead."

Indexes and snapshot isolation

"One option is to have the index simply point to all versions of an object and require an index query to filter out any object versions that are not visible to the current transaction."

Repeatable read and naming confusion

"PostgreSQL and MySQL call their snapshot isolation level repeatable read because it meets the requirements of the standard, and so they can claim standards compliance."

"As a result, nobody really knows what repeatable read means."

Preventing Lost Updates

"The read committed and snapshot isolation levels we’ve discussed so far have been primarily about the guarantees of what a read-only transaction can see in the presence of concurrent writes."

Atomic write operations

"Many databases provide atomic update operations, which remove the need to implement read-modify-write cycles in application code."

Explicit locking

"Another option for preventing lost updates, if the database’s built-in atomic operations don’t provide the necessary functionality, is for the application to explicitly lock objects that are going to be updated."

Automatically detecting lost updates

"An alternative is to allow them to execute in parallel and, if the transaction manager detects a lost update, abort the transaction and force it to retry its read-modify-write cycle."

Compare-and-set

"The purpose of this operation is to avoid lost updates by allowing an update to happen only if the value has not changed since you last read it."

Conflict resolution and replication

"However, databases with multi-leader or leaderless replication usually allow several writes to happen concurrently and replicate them asynchronously, so they cannot guarantee that there is a single up-to-date copy of the data."

"Thus, techniques based on locks or compare-and-set do not apply in this context."

Write Skew and Phantoms

"In each transaction, your application first checks that two or more doctors are currently on call; if yes, it assumes it’s safe for one doctor to go off call."

Characterizing write skew

"It is neither a dirty write nor a lost update, because the two transactions are updating two different objects (Alice’s and Bob’s on- call records, respectively)."

More examples of write skew

"Unfortunately, snapshot isolation does not prevent another user from concurrently inserting a conflicting meeting."

Phantoms causing write skew

"This effect, where a write in one transaction changes the result of a search query in another transaction, is called a phantom."

"Snapshot isolation avoids phantoms in read-only queries, but in read-write transactions like the examples we discussed, phantoms can lead to particularly tricky cases of write skew."

Materializing conflicts

"If the problem of phantoms is that there is no object to which we can attach the locks, perhaps we can artificially introduce a lock object into the database?"

"Note that the additional table isn’t used to store information about the booking—it’s purely a collection of locks which is used to prevent bookings on the same room and time range from being modified concurrently."

"This approach is called materializing conflicts, because it takes a phantom and turns it into a lock conflict on a concrete set of rows that exist in the database."

Serializability

"Some race conditions are prevented by the read committed and snapshot isolation levels, but others are not."

"Serializable isolation is usually regarded as the strongest isolation level."

Actual Serial Execution

"The simplest way of avoiding concurrency problems is to remove the concurrency entirely: to execute only one transaction at a time, in serial order, on a single thread."

"A system designed for single-threaded execution can sometimes perform better than a system that supports concurrency, because it can avoid the coordination overhead of locking."

Encapsulating transactions in stored procedures

"If you were to disallow concurrency in the database and only process one transaction at a time, the throughput would be dreadful because the database would spend most of its time waiting for the application to issue the next query for the current transaction."

"For this reason, systems with single-threaded serial transaction processing don’t allow interactive multi-statement transactions."

"Instead, the application must submit the entire transaction code to the database ahead of time, as a stored procedure."

Pros and cons of stored procedures

"These languages haven’t kept up with developments in general-purpose programming languages, so they look quite ugly and archaic from today’s point of view, and they lack the ecosystem of libraries that you find with most programming languages."

"Modern implementations of stored procedures have abandoned PL/SQL and use existing general-purpose programming languages instead: VoltDB uses Java or Groovy, Datomic uses Java or Clojure, and Redis uses Lua."

Partitioning

"Since cross-partition transactions have additional coordination overhead, they are vastly slower than single-partition transactions."

Summary of serial execution

"Every transaction must be small and fast, because it takes only one slow transaction to stall all transaction processing."

"It is limited to use cases where the active dataset can fit in memory."

"Cross-partition transactions are possible, but there is a hard limit to the extent to which they can be used."

Two-Phase Locking (2PL)

"If transaction A has read an object and transaction B wants to write to that object, B must wait until A commits or aborts before it can continue. (This ensures that B can’t change the object unexpectedly behind A’s back.)"

"If transaction A has written an object and transaction B wants to read that object, B must wait until A commits or aborts before it can continue."

Implementation of two-phase locking

"If a transaction wants to read an object, it must first acquire the lock in shared mode."

"If a transaction wants to write to an object, it must first acquire the lock in exclusive mode."

"This is where the name “two-phase” comes from: the first phase (while the transaction is executing) is when the locks are acquired, and the second phase (at the end of the transaction) is when all the locks are released."

"Since so many locks are in use, it can happen quite easily that transaction A is stuck waiting for transaction B to release its lock, and vice versa."

"This situation is called deadlock."

Performance of two-phase locking

"By design, if two concurrent transactions try to do anything that may in any way result in a race condition, one has to wait for the other to complete."

Predicate locks

"The key idea here is that a predicate lock applies even to objects that do not yet exist in the database, but which might be added in the future (phantoms)."

"If two-phase locking includes predicate locks, the database prevents all forms of write skew and other race conditions, and so its isolation becomes serializable."

Index-range locks

"Index-range locks are not as precise as predicate locks would be (they may lock a bigger range of objects than is strictly necessary to maintain serializability), but since they have much lower overheads, they are a good compromise."

Serializable Snapshot Isolation (SSI)

"Are serializable isolation and good performance fundamentally at odds with each other?"

Pessimistic versus optimistic concurrency control

"Two-phase locking is a so-called pessimistic concurrency control mechanism: it is based on the principle that if anything might possibly go wrong (as indicated by a lock held by another transaction), it’s better to wait until the situation is safe again before doing anything."

"It is like mutual exclusion, which is used to protect data structures in multi-threaded programming.

"Serial execution is, in a sense, pessimistic to the extreme: it is essentially equivalent to each transaction having an exclusive lock on the entire database (or one partition of the database) for the duration of the transaction."

"By contrast, serializable snapshot isolation is an optimistic concurrency control technique."

Decisions based on an outdated premise

"Put another way, the transaction is taking an action based on a premise (a fact that was true at the beginning of the transaction, e.g., “There are currently two doctors on call”)."

Detecting stale MVCC reads

"Recall that snapshot isolation is usually implemented by multi-version concurrency control (MVCC)."

"By avoiding unnecessary aborts, SSI preserves snapshot isolation’s support for long-running reads from a consistent snapshot."

Detecting writes that affect prior reads

"This process is similar to acquiring a write lock on the affected key range, but rather than blocking until the readers have committed, the lock acts as a tripwire: it simply notifies the transactions that the data they read may no longer be up to date."

Performance of serializable snapshot isolation

"Compared to two-phase locking, the big advantage of serializable snapshot isolation is that one transaction doesn’t need to block waiting for locks held by another transaction."

Summary

"Transactions are an abstraction layer that allows an application to pretend that certain concurrency problems and certain kinds of hardware and software faults don’t exist."

Dirty reads

"One client reads another client’s writes before they have been committed."

Dirty writes

"One client overwrites data that another client has written, but not yet committed."

Read skew (nonrepeatable reads)

"A client sees different parts of the database at different points in time."

Lost updates

"Two clients concurrently perform a read-modify-write cycle."

Write skew

"However, by the time the write is made, the premise of the decision is no longer true."

Phantom reads

"Another client makes a write that affects the results of that search."

"Only serializable isolation protects against all of these issues."

Literally executing transactions in a serial order

"If you can make each transaction very fast to execute, and the transaction throughput is low enough to process on a single CPU core, this is a simple and effective option."

Two-phase locking

"For decades this has been the standard way of implementing serializability, but many applications avoid using it because of its performance characteristics."

Serializable snapshot isolation (SSI)

"A fairly new algorithm that avoids most of the downsides of the previous approaches."

"When a transaction wants to commit, it is checked, and it is aborted if the execution was not serializable."

### 8 The Trouble with Distributed Systems

"We will now turn our pessimism to the maximum and assume that anything that can go wrong will go wrong."

Faults and Partial Failures

"There is no fundamental reason why software on a single computer should be flaky: when the hardware is working correctly, the same operation always produces the same result (it is deterministic)."

"This is a deliberate choice in the design of computers: if an internal fault occurs, we prefer a computer to crash completely rather than returning a wrong result, because wrong results are difficult and confusing to deal with."

"The difficulty is that partial failures are nondeterministic: if you try to do anything involving multiple nodes and the network, it may sometimes work and sometimes unpredictably fail."

Cloud Computing and Supercomputing

"Thus, a supercomputer is more like a single-node computer than a distributed system: it deals with partial failure by letting it escalate into total failure — if any part of the system fails, just let everything crash (like a kernel panic on a single machine)."

Building a Reliable System from Unreliable Components

"Error-correcting codes allow digital data to be transmitted accurately across a communication channel that occasionally gets some bits wrong, for example due to radio interference on a wireless network."

"IP (the Internet Protocol) is unreliable: it may drop, delay, duplicate, or reorder packets. TCP (the Transmission Control Protocol) provides a more reliable transport layer on top of IP: it ensures that missing packets are retransmitted, duplicates are eliminated, and packets are reassembled into the order in which they were sent."

Unreliable Networks

"The internet and most internal networks in datacenters (often Ethernet) are asynchronous packet networks."

"The sender can’t even tell whether the packet was delivered: the only option is for the recipient to send a response message, which may in turn be lost or delayed."

"However, when a timeout occurs, you still don’t know whether the remote node got your request or not (and if the request is still queued somewhere, it may still be delivered to the recipient, even if the sender has given up on it)."

Network Faults in Practice

"Sharks might bite undersea cables and damage them."

Detecting Faults

"If a node process crashed (or was killed by an administrator) but the node’s operating system is still running, a script can notify other nodes about the crash so that another node can take over quickly without having to wait for a timeout to expire."

Timeouts and Unbounded Delays

"A short timeout detects faults faster, but carries a higher risk of incorrectly declaring a node dead when in fact it has only suffered a temporary slowdown (e.g., due to a load spike on the node or the network)."

"In particular, it could happen that the node actually wasn’t dead but only slow to respond due to overload; transferring its load to other nodes can cause a cascading failure (in the extreme case, all nodes declare each other dead, and everything stops working)."

Network congestion and queueing

"TCP performs flow control (also known as congestion avoidance or backpressure), in which a node limits its own rate of sending in order to avoid overloading a network link or the receiving node."

TCP Versus UDP

"It’s a trade-off between reliability and variability of delays: as UDP does not perform flow control and does not retransmit lost packets, it avoids some of the reasons for variable network delays (although it is still susceptible to switch queues and scheduling delays)."

"In this case, there’s no point in retransmitting the packet — the application must instead fill the missing packet’s time slot with silence (causing a brief interruption in the sound) and move on in the stream."

Synchronous Versus Asynchronous Networks

"When you make a call over the telephone network, it establishes a circuit: a fixed, guaranteed amount of bandwidth is allocated for the call, along the entire route between the two callers."

"This kind of network is synchronous: even as data passes through several routers, it does not suffer from queueing, because the 16 bits of space for the call have already been reserved in the next hop of the network"

Can we not simply make network delays predictable?

"However, they are not: Ethernet and IP are packet-switched protocols, which suffer from queueing and thus unbounded delays in the network."

"The answer is that they are optimized for bursty traffic."

"Thus, using circuits for bursty data transfers wastes network capacity and makes transfers unnecessarily slow."

"By contrast, TCP dynamically adapts the rate of data transfer to the available network capacity."

"With careful use of quality of service (QoS, prioritization and scheduling of packets) and admission control (rate-limiting senders), it is possible to emulate circuit switching on packet networks, or provide statistically bounded delay."

Latency and Resource Utilization

"More generally, you can think of variable delays as a consequence of dynamic resource partitioning."

"Senders push and jostle with each other to get their packets over the wire as quickly as possible, and the network switches decide which packet to send (i.e., the bandwidth allocation) from one moment to the next."

"This approach has the downside of queueing, but the advantage is that it maximizes utilization of the wire."

"Variable delays in networks are not a law of nature, but simply the result of a cost/ benefit trade-off."

Unreliable Clocks

"It is possible to synchronize clocks to some degree: the most commonly used mechanism is the Network Time Protocol (NTP), which allows the computer clock to be adjusted according to the time reported by a group of servers."

Monotonic Versus Time-of-Day Clocks

Time-of-day clocks

"A time-of-day clock does what you intuitively expect of a clock: it returns the current date and time according to some calendar (also known as wall-clock time)."

"In particular, if the local clock is too far ahead of the NTP server, it may be forcibly reset and appear to jump back to a previous point in time."

"These jumps, as well as the fact that they often ignore leap seconds, make time-of-day clocks unsuitable for measuring elapsed time."

Monotonic clocks

"The name comes from the fact that they are guaranteed to always move forward (whereas a time-of- day clock may jump back in time)."

"In particular, it makes no sense to compare monotonic clock values from two different computers, because they don’t mean the same thing."

Clock Synchronization and Accuracy

"For example, the MiFID II draft European regulation for financial institutions requires all high-frequency trading funds to synchronize their clocks to within 100 microseconds of UTC, in order to help debug market anomalies such as “flash crashes” and to help detect market manipulation."

Relying on Synchronized Clocks

"Any node whose clock drifts too far from the others should be declared dead and removed from the cluster."

Timestamps for ordering events

"When node 2 receives these two events, it will incorrectly conclude that x = 1 is the more recent value and drop the write x = 2."

"Additional causality tracking mechanisms, such as version vectors, are needed in order to prevent violations of causality."

"Logical clocks do not measure the time of day or the number of seconds elapsed, only the relative ordering of events (whether one event happened before or after another)."

"In contrast, time-of-day and monotonic clocks, which measure actual elapsed time, are also known as physical clocks."

Clock readings have a confidence interval

"With an NTP server on the public internet, the best possible accuracy is probably to the tens of milliseconds, and the error may easily spike to over 100 ms when there is network congestion."

"Thus, it doesn’t make sense to think of a clock reading as a point in time — it is more like a range of times, within a confidence interval: for example, a system may be 95% confident that the time now is between 10.3 and 10.5 seconds past the minute, but it doesn’t know any more precisely than that."

"Unfortunately, most systems don’t expose this uncertainty: for example, when you call `clock_gettime()`, the return value doesn’t tell you the expected error of the timestamp, so you don’t know if its confidence interval is five milliseconds or five years."

"An interesting exception is Google’s TrueTime API in Spanner, which explicitly reports the confidence interval on the local clock."

"When you ask it for the current time, you get back two values: `[earliest, latest]`, which are the earliest possible and the latest possible timestamp."

Synchronized clocks for global snapshots

"If a write happened later than the snapshot (i.e., the write has a greater transaction ID than the snapshot), that write is invisible to the snapshot transaction."

"In order to ensure that transaction timestamps reflect causality, Spanner deliberately waits for the length of the confidence interval before committing a read-write trans‐ action."

"In order to keep the wait time as short as possible, Spanner needs to keep the clock uncertainty as small as possible; for this purpose, Google deploys a GPS receiver or atomic clock in each datacenter, allowing clocks to be synchronized to within about 7 ms."

"These ideas are interesting, but they have not yet been implemented in mainstream databases outside of Google."

Process Pauses

"How does a node know that it is still leader (that it hasn’t been declared dead by the others), and that it may safely accept writes?"

"In order to remain leader, the node must periodically renew the lease before it expires."

"When writing multi-threaded code on a single machine, we have fairly good tools for making it thread-safe: mutexes, semaphores, atomic counters, lock-free data structures, blocking queues, and so on."

"Unfortunately, these tools don’t directly translate to distributed systems, because a distributed system has no shared memory—only messages sent over an unreliable network."

Response time guarantees

"Some software runs in environments where a failure to respond within a specified time can cause serious damage: computers that control aircraft, rockets, robots, cars, and other physical objects must respond quickly and predictably to their sensor inputs."

"In these systems, there is a specified deadline by which the software must respond; if it doesn’t meet the deadline, that may cause a failure of the entire system."

"These are so-called hard real-time systems."

"For example, if your car’s onboard sensors detect that you are currently experiencing a crash, you wouldn’t want the release of the airbag to be delayed due to an inopportune GC pause in the airbag release system."

Limiting the impact of garbage collection

"An emerging idea is to treat GC pauses like brief planned outages of a node, and to let other nodes handle requests from clients while one node is collecting its garbage."

Knowledge, Truth, and Lies

"In a distributed system, we can state the assumptions we are making about the behavior (the system model) and design the actual system in such a way that it meets those assumptions."

The Truth Is Defined by the Majority

"A distributed system cannot exclusively rely on a single node, because a node may fail at any time, potentially leaving the system stuck and unable to recover."

"Most commonly, the quorum is an absolute majority of more than half the nodes (although other kinds of quorums are possible)."

The leader and the lock

"When the paused client comes back, it believes (incorrectly) that it still has a valid lease and proceeds to also write to the file."

Fencing tokens

"Let’s assume that every time the lock server grants a lock or lease, it also returns a fencing token, which is a number that increases every time a lock is granted (e.g., incremented by the lock service)."

Byzantine Faults

"Fencing tokens can detect and block a node that is inadvertently acting in error (e.g., because it hasn’t yet found out that its lease has expired)."

The Byzantine Generals Problem

"Most of the generals are loyal, and thus send truthful messages, but the traitors may try to deceive and confuse the others by sending fake or untrue messages (while trying to remain undiscovered)."

Weak forms of lying

"Although we assume that nodes are generally honest, it can be worth adding mechanisms to software that guard against weak forms of “lying” — for example, invalid messages due to hardware issues, software bugs, and misconfiguration."

System Model and Reality

"We do this by defining a system model, which is an abstraction that describes what things an algorithm may assume."

Correctness of an algorithm

"For example, the output of a sorting algorithm has the property that for any two distinct elements of the output list, the element further to the left is smaller than the element further to the right."

Safety and liveness

"In the example just given, uniqueness and monotonic sequence are safety properties, but availability is a liveness property."

"Safety is often informally defined as nothing bad happens, and liveness as something good eventually happens."

Mapping system models to the real world

"Proving an algorithm correct does not mean its implementation on a real system will necessarily always behave correctly."

Summary

"Whenever you try to send a packet over the network, it may be lost or arbitrarily delayed."

"A node’s clock may be significantly out of sync with other nodes (despite your best efforts to set up NTP), it may suddenly jump forward or back in time, and relying on it is dangerous because you most likely don’t have a good measure of your clock’s error interval."

"A process may pause for a substantial amount of time at any point in its execution (perhaps due to a stop-the-world garbage collector), be declared dead by other nodes, and then come back to life again without realizing that it was paused."

"In distributed systems, we try to build tolerance of partial failures into software, so that the system as a whole may continue functioning even when some of its constituent parts are broken."

### 9 Consistency and Consensus

"The best way of building fault-tolerant systems is to find some general-purpose abstractions with useful guarantees, implement them once, and then let applications rely on those guarantees."

"Even though crashes, race conditions, and disk failures do occur, the transaction abstraction hides those problems so that the application doesn’t need to worry about them."

Consistency Guarantees

"Most replicated databases provide at least eventual consistency, which means that if you stop writing to the database and wait for some unspecified length of time, then eventually all read requests will return the same value."

Linearizability

"Wouldn’t it be a lot simpler if the database could give the illusion that there is only one replica (i.e., only one copy of the data)?"

"In a linearizable system, as soon as one client successfully completes a write, all clients reading from the database must be able to see the value just written."

What Makes a System Linearizable?

"It is possible (though computationally expensive) to test whether a system’s behavior is linearizable by recording the timings of all requests and responses, and checking whether they can be arranged into a valid sequential order."

Linearizability Versus Serializability

Serializability

"It guarantees that transactions behave the same as if they had executed in some serial order (each transaction running to completion before the next transaction starts)."

Linearizability

"Linearizability is a recency guarantee on reads and writes of a register (an individual object)."

"A database may provide both serializability and linearizability, and this combination is known as strict serializability or strong one-copy serializability (strong-1SR)."

"The whole point of a consistent snapshot is that it does not include writes that are more recent than the snapshot, and thus reads from the snapshot are not linearizable."

Relying on Linearizability

Locking and leader election

"A system that uses single-leader replication needs to ensure that there is indeed only one leader, not several (split brain)."

"One way of electing a leader is to use a lock: every node that starts up tries to acquire the lock, and the one that succeeds becomes the leader."

Constraints and uniqueness guarantees

"If you want to enforce this constraint as the data is written (such that if two people try to concurrently create a user or a file with the same name, one of them will be returned an error), you need linearizability."

"This situation is actually similar to a lock: when a user registers for your service, you can think of them acquiring a “lock” on their chosen username."

Cross-channel timing dependencies

"If it processes an old version of the image, the full-size and resized images in the file storage become permanently inconsistent."

"Without the recency guarantee of linearizability, race conditions between these two channels are possible."

Implementing Linearizable Systems

Single-leader replication (potentially linearizable)

"If you make reads from the leader, or from synchronously updated followers, they have the potential to be linearizable."

Consensus algorithms (linearizable)

"However, consensus protocols contain measures to prevent split brain and stale replicas."

Multi-leader replication (not linearizable)

"Systems with multi-leader replication are generally not linearizable, because they concurrently process writes on multiple nodes and asynchronously replicate them to other nodes."

Leaderless replication (probably not linearizable)

"Even with strict quorums, nonlinearizable behavior is possible, as demonstrated in the next section."

Linearizability and quorums

"The quorum condition is met (w + r > n), but this execution is nevertheless not linearizable: B’s request begins after A’s request completes, but B returns the old value while A returns the new value."

The Cost of Linearizability

"If the network between datacenters is interrupted in a single-leader setup, clients connected to follower datacenters cannot contact the leader, so they cannot make any writes to the database, nor any linearizable reads."

"If the application requires linearizable reads and writes, the network interruption causes the application to become unavailable in the datacenters that cannot contact the leader."

The CAP theorem

"This issue is not just a consequence of single-leader and multi-leader replication: any linearizable database has this problem, no matter how it is implemented."

"If your application does not require linearizability, then it can be written in a way that each replica can process requests independently, even if it is disconnected from other replicas (e.g., multi-leader)."

"CAP was originally proposed as a rule of thumb, without precise definitions, with the goal of starting a discussion about trade-offs in databases."

The Unhelpful CAP Theorem

"CAP is sometimes presented as Consistency, Availability, Partition tolerance: pick 2 out of 3."

"When a network fault occurs, you have to choose between either linearizability or total availability. Thus, a better way of phrasing CAP would be either Consistent or Available when Partitioned."

Linearizability and network delays

"The reason for dropping linearizability is performance, not fault tolerance."

Ordering Guarantees

"It turns out that there are deep connections between ordering, linearizability, and consensus."

Ordering and Causality

"There are several reasons why ordering keeps coming up, and one of the reasons is that it helps preserve causality."

"If a system obeys the ordering imposed by causality, we say that it is causally consistent."

The causal order is not a total order

"A total order allows any two elements to be compared, so if you have two elements, you can always say which one is greater and which one is smaller."

"We say they are incomparable, and therefore mathematical sets are partially ordered: in some cases one set is greater than another (if one set contains all the elements of another), but in other cases they are incomparable."

Linearizability

"In a linearizable system, we have a total order of operations: if the system behaves as if there is only a single copy of the data, and every operation is atomic, this means that for any two operations we can always say which one happened first."

Causality

"Put another way, two events are ordered if they are causally related (one happened before the other), but they are incomparable if they are concurrent."

Linearizability is stronger than causal consistency

"The fact that linearizability ensures causality is what makes linearizable systems simple to understand and appealing."

Capturing causal dependencies

"In order to determine the causal ordering, the database needs to know which version of the data was read by the application."

Sequence Number Ordering

"However, there is a better way: we can use sequence numbers or timestamps to order events."

"Such sequence numbers or timestamps are compact (only a few bytes in size), and they provide a total order: that is, every operation has a unique sequence number, and you can always compare two sequence numbers to determine which is greater (i.e., which operation happened later)."

Noncausal sequence number generators

"If there is not a single leader (perhaps because you are using a multi-leader or leaderless database, or because the database is partitioned), it is less clear how to generate sequence numbers for operations."

Lamport timestamps

"The Lamport timestamp is then simply a pair of (counter, node ID)."

Timestamp ordering is not sufficient

"Although Lamport timestamps define a total order of operations that is consistent with causality, they are not quite sufficient to solve many common problems in distributed systems."

"If you have an operation to create a username, and you are sure that no other node can insert a claim for the same username ahead of your operation in the total order, then you can safely declare the operation successful."

Total Order Broadcast

"Total order broadcast is usually described as a protocol for exchanging messages between nodes."

Reliable delivery

"No messages are lost: if a message is delivered to one node, it is delivered to all nodes."

Totally ordered delivery

"Messages are delivered to every node in the same order."

"A correct algorithm for total order broadcast must ensure that the reliability and ordering properties are always satisfied, even if a node or the network is faulty."

Using total order broadcast

"The sequence number can then serve as a fencing token, because it is monotonically increasing."

Implementing linearizable storage using total order broadcast

"However, if you have total order broadcast, you can build linearizable storage on top of it."

Implementing total order broadcast using linearizable storage

"The easiest way is to assume you have a linearizable register that stores an integer and that has an atomic increment-and-get operation."

Distributed Transactions and Consensus

"Consensus is one of the most important and fundamental problems in distributed computing."

"On the surface, it seems simple: informally, the goal is simply to get several nodes to agree on something."

The Impossibility of Consensus

"You may have heard about the FLP result — named after the authors Fischer, Lynch, and Paterson — which proves that there is no algorithm that is always able to reach consensus if there is a risk that a node may crash."

"Even just allowing the algorithm to use random numbers is sufficient to get around the impossibility result."

Atomic Commit and Two-Phase Commit (2PC)

"Atomicity prevents failed transactions from littering the database with half-finished results and half-updated state."

From single-node to distributed atomic commit

"Thus, on a single node, transaction commitment crucially depends on the order in which data is durably written to disk: first the data, then the commit record."

Introduction to two-phase commit

"Two-phase commit is an algorithm for achieving atomic transaction commit across multiple nodes — i.e., to ensure that either all nodes commit or all nodes abort."

"2PC uses a new component that does not normally appear in single-node transactions: a coordinator (also known as transaction manager)."

"When the application is ready to commit, the coordinator begins phase 1: it sends a prepare request to each of the nodes, asking them whether they are able to commit."

"If all participants reply “yes,” indicating they are ready to commit, then the coordinator sends out a commit request in phase 2, and the commit actually takes place."

"If any of the participants replies “no,” the coordinator sends an abort request to all nodes in phase 2."

"This process is somewhat like the traditional marriage ceremony in Western cultures: the minister asks the bride and groom individually whether each wants to marry the other, and typically receives the answer “I do” from both."

"After receiving both acknowledgments, the minister pronounces the couple husband and wife: the transaction is committed, and the happy fact is broadcast to all attendees."

A system of promises

"Surely the prepare and commit requests can just as easily be lost in the two-phase case."

"Thus, the protocol contains two crucial “points of no return”: when a participant votes “yes,” it promises that it will definitely be able to commit later (although the coordinator may still choose to abort); and once the coordinator decides, that decision is irrevocable."

"When you recover consciousness later, you can find out whether you are married or not by querying the minister for the status of your global transaction ID, or you can wait for the minister’s next retry of the commit request (since the retries will have continued throughout your period of unconsciousness)."

Coordinator failure

"If the coordinator crashes or the network fails at this point, the participant can do nothing but wait. A participant’s transaction in this state is called in doubt or uncertain."

Three-phase commit

"In general, nonblocking atomic commit requires a perfect failure detector — i.e., a reliable mechanism for telling whether a node has crashed or not."

"In a network with unbounded delay a timeout is not a reliable failure detector, because a request may time out due to a network problem even if no node has crashed."

Distributed Transactions in Practice

"Some implementations of distributed transactions carry a heavy performance penalty — for example, distributed transactions in MySQL are reported to be over 10 times slower than single-node transactions, so it is not surprising when people advise against using them."

Database-internal distributed transactions

"In this case, all the nodes participating in the transaction are running the same database software."

Heterogeneous distributed transactions

"In a heterogeneous transaction, the participants are two or more different technologies: for example, two databases from different vendors, or even non-database systems such as message brokers."

Exactly-once message processing

"If either the message delivery or the database transaction fails, both are aborted, and so the message broker may safely redeliver the message later."

"Thus, by atomically committing the message and the side effects of its processing, we can ensure that the message is effectively processed exactly once, even if it required a few retries before it succeeded."

XA transactions

"X/Open XA (short for eXtended Architecture) is a standard for implementing two-phase commit across heterogeneous technologies."

Holding locks while in doubt

"Therefore, when using two-phase commit, a transaction must hold onto the locks throughout the time it is in doubt."

"If the coordinator has crashed and takes 20 minutes to start up again, those locks will be held for 20 minutes."

Recovering from coordinator failure

"Resolving the problem potentially requires a lot of manual effort, and most likely needs to be done under high stress and time pressure during a serious production outage (otherwise, why would the coordinator be in such a bad state?)."

"To be clear, heuristic here is a euphemism for probably breaking atomicity, since it violates the system of promises in two-phase commit."

Limitations of distributed transactions

"In particular, the key realization is that the transaction coordinator is itself a kind of database (in which transaction outcomes are stored), and so it needs to be approached with the same care as any other important database:"

"If the coordinator is not replicated but runs only on a single machine, it is a sin‐ gle point of failure for the entire system (since its failure causes other application servers to block on locks held by in-doubt transactions)."

Fault-Tolerant Consensus

"The consensus problem is normally formalized as follows: one or more nodes may propose values, and the consensus algorithm decides on one of those values."

"Thus, a large-scale outage can stop the system from being able to process requests, but it cannot corrupt the consensus system by causing it to make invalid decisions."

Consensus algorithms and total order broadcast

"So, total order broadcast is equivalent to repeated rounds of consensus (each consensus decision corresponding to one message delivery):"

"Due to the agreement property of consensus, all nodes decide to deliver the same messages in the same order."

Single-leader replication and consensus

"If the leader is manually chosen and configured by the humans in your operations team, you essentially have a “consensus algorithm” of the dictatorial variety: only one node is allowed to accept writes (i.e., make decisions about the order of writes in the replication log), and if that node goes down, the system becomes unavailable for writes until the operators manually configure a different node to be the leader."

Epoch numbering and quorums

"This election is given an incremented epoch number, and thus epoch numbers are totally ordered and monotonically increasing."

"Thus, we have two rounds of voting: once to choose a leader, and a second time to vote on a leader’s proposal."

Limitations of consensus

"Consensus algorithms are a huge breakthrough for distributed systems: they bring concrete safety properties (agreement, integrity, and validity) to systems where everything else is uncertain, and they nevertheless remain fault-tolerant (able to make progress as long as a majority of nodes are working and reachable)."

Membership and Coordination Services

"Projects like ZooKeeper or etcd are often described as “distributed key-value stores” or “coordination and configuration services.”"

"It is more likely that you will end up relying on it indirectly via some other project: for example, HBase, Hadoop YARN, OpenStack Nova, and Kafka all rely on ZooKeeper running in the background."

"ZooKeeper is modeled after Google’s Chubby lock service, implementing not only total order broadcast (and hence consensus), but also an interesting set of other features that turn out to be particularly useful when building distributed systems:"

Linearizable atomic operations

"Using an atomic compare-and-set operation, you can implement a lock: if several nodes concurrently try to perform the same operation, only one of them will succeed."

Total ordering of operations

"The fencing token is some number that monotonically increases every time the lock is acquired."

Failure detection

"Clients maintain a long-lived session on ZooKeeper servers, and the client and server periodically exchange heartbeats to check that the other node is still alive."

Change notifications

"By subscribing to notifications, a client avoids having to frequently poll to find out about changes."

Allocating work to nodes

"One example in which the ZooKeeper/Chubby model works well is if you have several instances of a process or service, and one of them needs to be chosen as leader or primary."

Service discovery

"Thus, if your consensus system already knows who the leader is, then it can make sense to also use that information to help other services discover who the leader is."

Membership services

"A membership service determines which nodes are currently active and live members of a cluster."

Summary

"Although linearizability is appealing because it is easy to understand — it makes a database behave like a variable in a single-threaded program — it has the downside of being slow, especially in environments with large network delays."

"Causal consistency does not have the coordination overhead of linearizability and is much less sensitive to network problems."

"Tools like ZooKeeper play an important role in providing an “outsourced” consensus, failure detection, and membership service that applications can use."

## III Derived Data

"In a large application you often need to be able to access and process data in many different ways, and there is no one database that can satisfy all those different needs simultaneously."

"Applications thus commonly use a combination of several different datastores, indexes, caches, analytics systems, etc. and implement mechanisms for moving data from one store to another."

Systems of Record and Derived Data

"A system of record, also known as source of truth, holds the authoritative version of your data."


"If there is any discrepancy between another system and the system of record, then the value in the system of record is (by definition) the correct one."

"Data in a derived system is the result of taking some existing data from another system and transforming or processing it in some way."

### 10 Batch Processing

"The web, and increasing numbers of HTTP/REST-based APIs, has made the request/ response style of interaction so common that it’s easy to take it for granted."

Services (online systems)

"A service waits for a request or instruction from a client to arrive. When one is received, the service tries to handle it as quickly as possible and sends a response back."

Batch processing systems (offline systems)

"A batch processing system takes a large amount of input data, runs a job to process it, and produces some output data."

Stream processing systems (near-real-time systems)

"Stream processing is somewhere between online and offline/batch processing (so it is sometimes called near-real-time or nearline processing)."

Batch Processing with Unix Tools

"So, this one line of the log indicates that on February 27, 2015, at 17:55:11 UTC, the server received a request for the file /css/typography.css from the client IP address 216.58.210.78."

Simple Log Analysis

```sh
cat /var/log/nginx/access.log |
  awk '{print $7}' |
  sort             |
  uniq -c          |
  sort -r -n       |
  head -n  5
```

"Surprisingly many data analyses can be done in a few minutes using some combination of awk, sed, grep, sort, uniq, and xargs, and they perform surprisingly well."

Chain of commands versus custom program

```ruby
counts = Hash.new(0)

File.open('/var/log/nginx/access.log') do |file|
    file.each do |line|
        url = line.split[6]
        counts[url] += 1 
    end
end

top5 = counts.map{|url, count| [count, url] }.sort.reverse[0...5]
top5.each{|count, url| puts "#{count} #{url}" }
```

"This program is not as concise as the chain of Unix pipes, but it’s fairly readable, and which of the two you prefer is partly a matter of taste."

Sorting versus in-memory aggregation

"The Ruby script keeps an in-memory hash table of URLs, where each URL is mapped to the number of times it has been seen."

"The Unix pipeline example does not have such a hash table, but instead relies on sorting a list of URLs in which multiple occurrences of the same URL are simply repeated."

"For most small to mid-sized websites, you can probably fit all distinct URLs, and a counter for each URL, in (say) 1 GB of memory."

"In this example, the working set of the job (the amount of memory to which the job needs random access) depends only on the number of distinct URLs: if there are a million log entries for a single URL, the space required in the hash table is still just one URL plus the size of the counter."

"The sort utility in GNU Coreutils (Linux) automatically handles larger-than- memory datasets by spilling to disk, and automatically parallelizes sorting across multiple CPU cores."

"This means that the simple chain of Unix commands we saw earlier easily scales to large datasets, without running out of memory."

"The bottleneck is likely to be the rate at which the input file can be read from disk."

The Unix Philosophy

"It’s no coincidence that we were able to analyze a log file quite easily, using a chain of commands like in the previous example: this was in fact one of the key design ideas of Unix, and it remains astonishingly relevant today."

"Doug McIlroy, the inventor of Unix pipes, first described them like this in 1964: “We should have some ways of connecting programs like [a] garden hose — screw in another segment when it becomes necessary to massage data in another way. This is the way of I/O also.”"

"The plumbing analogy stuck, and the idea of connecting programs with pipes became part of what is now known as the Unix philosophy — a set of design principles that became popular among the developers and users of Unix."

> 1. Make each program do one thing well. To do a new job, build afresh rather than complicate old programs by adding new “features”.
> 2. Expect the output of every program to become the input to another, as yet unknown, program. Don’t clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don’t insist on interactive input.
> 3. Design and build software, even operating systems, to be tried early, ideally within weeks. Don’t hesitate to throw away the clumsy parts and rebuild them.
> 4. Use tools in preference to unskilled help to lighten a programming task, even if you have to detour to build the tools and expect to throw some of them out after you’ve finished using them.

"The sort tool is a great example of a program that does one thing well."

"It is arguably a better sorting implementation than most programming languages have in their standard libraries (which do not spill to disk and do not use multiple threads, even when that would be beneficial)."

"And yet, sort is barely useful in isolation."

"It only becomes powerful in combination with the other Unix tools, such as uniq."

A uniform interface

"If you want to be able to connect any program’s output to any program’s input, that means that all programs must use the same input/output interface."

"Because that is such a simple interface, many different things can be represented using the same interface: an actual file on the filesystem, a communication channel to another process (Unix socket, stdin, stdout), a device driver (say /dev/audio or /dev/lp0), a socket representing a TCP connection, and so on."

"Our log analysis example used this fact: awk, sort, uniq, and head all treat their input file as a list of records separated by the \n (newline, ASCII 0x0A) character."

"Not many pieces of software interoperate and compose as well as Unix tools do: you can’t easily pipe the contents of your email account and your online shopping history through a custom analysis tool into a spreadsheet and post the results to a social network or a wiki."

"Today it’s an exception, not the norm, to have programs that work together as smoothly as Unix tools do."

Separation of logic and wiring

"Programs that need multiple inputs or outputs are possible but tricky."

"You can’t pipe a program’s output into a network connection."

Transparency and experimentation

"The input files to Unix commands are normally treated as immutable."

"You can end the pipeline at any point, pipe the output into less, and look at it to see if it has the expected form."

"You can write the output of one pipeline stage to a file and use that file as input to the next stage."

MapReduce and Distributed Filesystems

"A single MapReduce job is comparable to a single Unix process: it takes one or more inputs and produces one or more outputs."

"While Unix tools use stdin and stdout as input and output, MapReduce jobs read and write files on a distributed filesystem."

"In Hadoop’s implementation of MapReduce, that filesystem is called HDFS (Hadoop Distributed File System), an open source reimplementation of the Google File System (GFS)."

"Thus, HDFS conceptually creates one big filesystem that can use the space on the disks of all machines running the daemon."

MapReduce Job Execution

"Viewed like this, the role of the mapper is to prepare the data by putting it into a form that is suitable for sorting, and the role of the reducer is to process the data that has been sorted."

Distributed execution of MapReduce

"The main difference from pipelines of Unix commands is that MapReduce can parallelize a computation across many machines, without you having to write code to explicitly handle the parallelism."

"The mapper and reducer only operate on one record at a time; they don’t need to know where their input is coming from or their output is going to, so the framework can handle the complexities of moving data between machines."

MapReduce workflows

"The Hadoop MapReduce framework does not have any particular support for workflows, so this chaining is done implicitly by directory name: the first job must be configured to write its output to a designated directory in HDFS, and the second job must be configured to read that same directory name as its input."

Reduce-Side Joins and Grouping

"When we talk about joins in the context of batch processing, we mean resolving all occurrences of some association within a dataset."

Example: analysis of user activity events

"Thus, a better approach would be to take a copy of the user database and to put it in the same distributed filesystem as the log of user activity events."

"You would then have the user database in one set of files in HDFS and the user activity records in another set of files, and could use MapReduce to bring together all of the relevant records in the same place and process them efficiently."

Sort-merge joins

"Since the reducer processes all of the records for a particular user ID in one go, it only needs to keep one user record in memory at any one time, and it never needs to make any requests over the network."

"This algorithm is known as a sort-merge join, since mapper output is sorted by key, and the reducers then merge together the sorted lists of records from both sides of the join."

Bringing related data together in the same place

"Using the MapReduce programming model has separated the physical network communication aspects of the computation (getting the data to the right machine) from the application logic (processing the data once you have it)."

"This separation contrasts with the typical use of databases, where a request to fetch data from a database often occurs somewhere deep inside a piece of application code."

"Since MapReduce handles all network communication, it also shields the application code from having to worry about partial failures, such as the crash of another node: MapReduce transparently retries failed tasks without affecting the application logic."

GROUP BY

"The simplest way of implementing such a grouping operation with MapReduce is to set up the mappers so that the key-value pairs they produce use the desired grouping key."

"Thus, grouping and joining look quite similar when implemented on top of MapReduce."

Handling skew

"Since a MapReduce job is only complete when all of its mappers and reducers have completed, any subsequent jobs must wait for the slowest reducer to complete before they can start."

"If a join input has hot keys, there are a few algorithms you can use to compensate."

Map-Side Joins

"The join algorithms described in the last section perform the actual join logic in the reducers, and are hence known as reduce-side joins."

"On the other hand, if you can make certain assumptions about your input data, it is possible to make joins faster by using a so-called map-side join."

Broadcast hash joins

"This simple but effective algorithm is called a broadcast hash join: the word broadcast reflects the fact that each mapper for a partition of the large input reads the entirety of the small input (so the small input is effectively “broadcast” to all partitions of the large input), and the word hash reflects its use of a hash table."

Partitioned hash joins

"If the partitioning is done correctly, you can be sure that all the records you might want to join are located in the same numbered partition, and so it is sufficient for each mapper to only read one partition from each of the input datasets."

Map-side merge joins

"Another variant of a map-side join applies if the input datasets are not only partitioned in the same way, but also sorted based on the same key."

MapReduce workflows with map-side joins

"Knowing about the physical layout of datasets in the distributed filesystem becomes important when optimizing join strategies: it is not sufficient to just know the encoding format and the name of the directory in which the data is stored; you must also know the number of partitions and the keys by which the data is partitioned and sorted."

The Output of Batch Workflows

"It is not transaction processing, nor is it analytics."

Building search indexes

"Google’s original use of MapReduce was to build indexes for its search engine, which was implemented as a workflow of 5 to 10 MapReduce jobs."

"Although Google later moved away from using MapReduce for this purpose, it helps to understand MapReduce if you look at it through the lens of building a search index."

"If you need to perform a full-text search over a fixed set of documents, then a batch process is a very effective way of building the indexes: the mappers partition the set of documents as needed, each reducer builds the index for its partition, and the index files are written to the distributed filesystem."

Key-value stores as batch process output

"Another common use for batch processing is to build machine learning systems such as classifiers (e.g., spam filters, anomaly detection, image recognition) and recommendation systems (e.g., people you may know, products you may be interested in, or related searches)."

"A much better solution is to build a brand-new database inside the batch job and write it as files to the job’s output directory in the distributed filesystem, just like the search indexes in the last section."

Philosophy of batch process outputs

"In these areas, the design principles that worked well for Unix also seem to be work‐ ing well for Hadoop — but Unix and Hadoop also differ in some ways."

Comparing Hadoop to Distributed Databases

"The biggest difference is that MPP databases focus on parallel execution of analytic SQL queries on a cluster of machines, while the combination of MapReduce and a distributed filesystem provides something much more like a general-purpose operating system that can run arbitrary programs."

Diversity of storage

"Indiscriminate data dumping shifts the burden of interpreting the data: instead of forcing the producer of a dataset to bring it into a standardized format, the interpretation of the data becomes the consumer’s problem."

Diversity of processing models

"If you have HDFS and MapReduce, you can build a SQL query execution engine on top of it, and indeed this is what the Hive project did."

"However, you can also write many other forms of batch processes that do not lend themselves to being expressed as a SQL query."

"Crucially, those various processing models can all be run on a single shared-use cluster of machines, all accessing the same files on the distributed filesystem."

Designing for frequent faults

"At Google, a MapReduce task that runs for an hour has an approximately 5% risk of being terminated to make space for a higher-priority process."

"This rate is more than an order of magnitude higher than the rate of failures due to hardware issues, machine reboot, or other reasons."

"At this rate of preemptions, if a job has 100 tasks that each run for 10 minutes, there is a risk greater than 50% that at least one task will be terminated before it is finished."

"And this is why MapReduce is designed to tolerate frequent unexpected task termination: it’s not because the hardware is particularly unreliable, it’s because the freedom to arbitrarily terminate processes enables better resource utilization in a computing cluster."

Beyond MapReduce

"Quite the opposite: implementing a complex processing job using the raw MapReduce APIs is actually quite hard and laborious — for instance, you would need to implement any join algorithms from scratch."

"In response to the difficulty of using MapReduce directly, various higher-level programming models (Pig, Hive, Cascading, Crunch) were created as abstractions on top of MapReduce."

"If you understand how MapReduce works, they are fairly easy to learn, and their higher-level constructs make many common batch processing tasks significantly easier to implement."

Materialization of Intermediate State

"In this case, the files on the distributed filesystem are simply intermediate state: a means of passing data from one job to the next."

"The process of writing out this intermediate state to files is called materialization."

"Pipes do not fully materialize the intermediate state, but instead stream the output to the input incrementally, using only a small in-memory buffer."

Dataflow engines

"Since they explicitly model the flow of data through several processing stages, these systems are known as dataflow engines."

Fault tolerance

"An advantage of fully materializing intermediate state to a distributed filesystem is that it is durable, which makes fault tolerance fairly easy in MapReduce: if a task fails, it can just be restarted on another machine and read the same input again from the filesystem."

"Spark, Flink, and Tez avoid writing intermediate state to HDFS, so they take a different approach to tolerating faults: if a machine fails and the intermediate state on that machine is lost, it is recomputed from other data that is still available (a prior intermediary stage if possible, or otherwise the original input data, which is normally on HDFS)."

"To enable this recomputation, the framework must keep track of how a given piece of data was computed — which input partitions it used, and which operators were applied to it."

"When recomputing data, it is important to know whether the computation is deterministic: that is, given the same input data, do the operators always produce the same output?"

Discussion of materialization

"Flink especially is built around the idea of pipelined execution: that is, incrementally passing the output of an operator to other operators, and not waiting for the input to be complete before starting to process it."

Graphs and Iterative Processing

"Many graph algorithms are expressed by traversing one edge at a time, joining one vertex with an adjacent vertex in order to propagate some information, and repeating until some condition is met — for example, until there are no more edges to follow, or until some metric converges."

"This approach works, but implementing it with MapReduce is often very inefficient, because MapReduce does not account for the iterative nature of the algorithm: it will always read the entire input dataset and produce a completely new output dataset, even if only a small part of the graph has changed compared to the last iteration."

The Pregel processing model

"In each iteration, a function is called for each vertex, passing it all the messages that were sent to it — much like a call to the reducer."

"It’s a bit similar to the actor model, if you think of each vertex as an actor, except that vertex state and messages between vertices are fault-tolerant and durable, and communication proceeds in fixed rounds: at every iteration, the framework delivers all messages sent in the previous iteration."

Fault tolerance

"This fault tolerance is achieved by periodically checkpointing the state of all vertices at the end of an iteration — i.e., writing their full state to durable storage."

Parallel execution

"Because the programming model deals with just one vertex at a time (sometimes called “thinking like a vertex”), the framework may partition the graph in arbitrary ways."

High-Level APIs and Languages

"These dataflow APIs generally use relational-style building blocks to express a computation: joining datasets on the value of some field; grouping tuples by key; filtering by some condition; and aggregating tuples by counting, summing, or other functions."

The move toward declarative query languages

"By incorporating declarative aspects in their high-level APIs, and having query optimizers that can take advantage of them during execution, batch processing frame‐ works begin to look more like MPP databases (and can achieve comparable performance)."

"At the same time, by having the extensibility of being able to run arbitrary code and read data in arbitrary formats, they retain their flexibility advantage."

Specialization for different domains

"As batch processing systems gain built-in functionality and high-level declarative operators, and as MPP databases become more programmable and flexible, the two are beginning to look more alike: in the end, they are all just systems for storing and processing data."

Summary

"In the Unix world, the uniform interface that allows one program to be composed with another is files and pipes; in MapReduce, that interface is a distributed filesystem."

"The output of mappers is repartitioned, sorted, and merged into a configurable number of reducer partitions."

"Dataflow engines perform less materialization of intermediate state and keep more in memory, which means that they need to recompute more data if a node fails."

"By partitioning, sorting, and merging, all the records with the same key end up going to the same call of the reducer."

"One of the two join inputs is small, so it is not partitioned and it can be entirely loaded into a hash table."

"If the two join inputs are partitioned in the same way (using the same key, same hash function, and same number of partitions), then the hash table approach can be used independently for each partition."

"Thanks to the framework, your code in a batch processing job does not need to worry about implementing fault-tolerance mechanisms: the framework can guarantee that the final output of a job is the same as if no faults had occurred, even though in reality various tasks perhaps had to be retried."

"The distinguishing feature of a batch processing job is that it reads some input data and produces some output data, without modifying the input — in other words, the output is derived from the input."

"Crucially, the input data is bounded: it has a known, fixed size (for example, it consists of a set of log files at some point in time, or a snapshot of a database’s contents)."

"Because it is bounded, a job knows when it has finished reading the entire input, and so a job eventually completes when it is done."

### 11 Stream Processing

"In reality, a lot of data is unbounded because it arrives gradually over time: your users produced data yesterday and today, and they will continue to produce more data tomorrow."

"In general, a “stream” refers to data that is incrementally made available over time."

Transmitting Event Streams

"In a filesystem, a filename identifies a set of related records; in a streaming system, related events are usually grouped together into a topic or stream."

Messaging Systems

"In particular, Unix pipes and TCP connect exactly one sender with one recipient, whereas a messaging system allows multiple producer nodes to send messages to the same topic and allows multiple consumer nodes to receive messages in a topic."

"What happens if the producers send messages faster than the consumers can pro‐ cess them?"

"Broadly speaking, there are three options: the system can drop messages, buffer messages in a queue, or apply backpressure (also known as flow control; i.e., blocking the producer from sending more messages)."

"What happens if nodes crash or temporarily go offline — are any messages lost? As with databases, durability may require some combination of writing to disk and/or replication, which has a cost."

Direct messaging from producers to consumers

"UDP multicast is widely used in the financial industry for streams such as stock market feeds, where low latency is important."

Message brokers

"A widely used alternative is to send messages via a message broker (also known as a message queue), which is essentially a kind of database that is optimized for handling message streams."

"It runs as a server, with producers and consumers connecting to it as clients."

"Producers write messages to the broker, and consumers receive them by reading them from the broker."

"A consequence of queueing is also that consumers are generally asynchronous: when a producer sends a message, it normally only waits for the broker to confirm that it has buffered the message and does not wait for the message to be processed by consumers."

Message brokers compared to databases

"Databases usually keep data until it is explicitly deleted, whereas most message brokers automatically delete a message when it has been successfully delivered to its consumers."

"If the broker needs to buffer a lot of messages because the consumers are slow (perhaps spilling messages to disk if they no longer fit in memory), each individual message takes longer to process, and the overall throughput may degrade."

Multiple consumers

Load balancing

"Each message is delivered to one of the consumers, so the consumers can share the work of processing the messages in the topic."

Fan-out

"Fan-out allows several independent consumers to each “tune in” to the same broadcast of messages, without affecting each other—the streaming equivalent of having several different batch jobs that read the same input file."

"The two patterns can be combined: for example, two separate groups of consumers may each subscribe to a topic, such that each group collectively receives all messages, but within each group only one of the nodes receives each message."

Acknowledgments and redelivery

"In order to ensure that the message is not lost, message brokers use acknowledgments: a client must explicitly tell the broker when it has finished processing a message so that the broker can remove it from the queue."

"When combined with load balancing, this redelivery behavior has an interesting effect on the ordering of messages."

Partitioned Logs

"Although it is possible to record it permanently (using packet capture and logging), we normally don’t think of it that way."

Using logs for message storage

"The same structure can be used to implement a message broker: a producer sends a message by appending it to the end of the log, and a consumer receives messages by reading the log sequentially."

Logs compared to traditional messaging

"Apache Kafka, Amazon Kinesis Streams, and Twitter’s DistributedLog are log-based message brokers that work like this."

"Google Cloud Pub/Sub is architecturally similar but exposes a JMS-style API rather than a log abstraction."

"Even though these message brokers write all messages to disk, they are able to achieve throughput of millions of messages per second by partitioning across multiple machines, and fault tolerance by replicating messages."

Logs compared to traditional messaging

"The log-based approach trivially supports fan-out messaging, because several consumers can independently read the log without affecting each other—reading a message does not delete it from the log."

Consumer offsets

"In database replication, the log sequence number allows a follower to reconnect to a leader after it has become disconnected, and resume replication without skipping any writes."

"Exactly the same principle is used here: the message broker behaves like a leader database, and the consumer like a follower."

Disk space usage

"Effectively, the log implements a bounded-size buffer that discards old messages when it gets full, also known as a circular buffer or ring buffer."

When consumers cannot keep up with producers

"If a consumer falls so far behind that the messages it requires are older than what is retained on disk, it will not be able to read those messages — so the broker effectively drops old messages that go back further than the size of the buffer can accommodate."

Replaying old messages

"But the offset is under the consumer’s control, so it can easily be manipulated if necessary: for example, you can start a copy of a consumer with yesterday’s offsets and write the output to a different location, in order to reprocess the last day’s worth of messages."

"This aspect makes log-based messaging more like the batch processes of the last chapter, where derived data is clearly separated from input data through a repeatable transformation process."

Databases and Streams

"Even though they have traditionally been considered separate categories of tools, we saw that log-based message brokers have been successful in taking ideas from databases and applying them to messaging."

"We can also go in reverse: take ideas from messaging and streams, and apply them to databases."

Keeping Systems in Sync

"As the same or related data appears in several different places, they need to be kept in sync with one another: if an item is updated in the database, it also needs to be updated in the cache, search indexes, and data warehouse."

"If periodic full database dumps are too slow, an alternative that is sometimes used is dual writes, in which the application code explicitly writes to each of the systems when data changes: for example, first writing to the database, then updating the search index, then invalidating the cache entries (or even performing those writes concurrently)."

"Due to unlucky timing, the requests are interleaved: the database first sees the write from client 1 setting the value to A, then the write from client 2 setting the value to B, so the final value in the database is B."

"The two systems are now permanently inconsistent with each other, even though no error occurred."

Change Data Capture

"More recently, there has been growing interest in change data capture (CDC), which is the process of observing all data changes written to a database and extracting them in a form in which they can be replicated to other systems."

Implementing change data capture

"Change data capture is a mechanism for ensuring that all changes made to the system of record are also reflected in the derived data systems so that the derived systems have an accurate copy of the data."

Initial snapshot

"Building a new full-text index, for example, requires a full copy of the entire database — it is not sufficient to only apply a log of recent changes, since it would be missing items that were not recently updated."

"The snapshot of the database must correspond to a known position or offset in the change log, so that you know at which point to start applying changes after the snapshot has been processed."

Log compaction

"The principle is simple: the storage engine periodically looks for log records with the same key, throws away any duplicates, and keeps only the most recent update for each key."

"The log is guaranteed to contain the most recent value for every key in the database (and maybe some older values) — in other words, you can use it to obtain a full copy of the database contents without having to take another snapshot of the CDC source database."

API support for change streams

"Once the stream of change events is in Kafka, it can be used to update derived data systems such as search indexes, and also feed into stream processing systems as discussed later in this chapter."

Event Sourcing

"There are some parallels between the ideas we’ve discussed here and event sourcing, a technique that was developed in the domain-driven design (DDD) community."

"Event sourcing is a powerful technique for data modeling: from an application point of view it is more meaningful to record the user’s actions as immutable events, rather than recording the effect of those actions on a mutable database."

Deriving current state from the event log

"Thus, applications that use event sourcing need to take the log of events (representing the data written to the system) and transform it into application state that is suitable for showing to a user (the way in which data is read from the system)."

Commands and events

"When a request from a user first arrives, it is initially a command: at this point it may still fail, for example because some integrity condition is violated."

"The application must first validate that it can execute the command."

"If the validation is successful and the command is accepted, it becomes an event, which is durable and immutable."

"At the point when the event is generated, it becomes a fact."

"Even if the customer later decides to change or cancel the reservation, the fact remains true that they formerly held a reservation for a particular seat, and the change or cancellation is a separate event that is added later."

State, Streams, and Immutability

"If you are mathematically inclined, you might say that the application state is what you get when you integrate an event stream over time, and a change stream is what you get when you differentiate the state by time."

"The analogy has limitations (for example, the second derivative of state does not seem to be meaningful), but it’s a useful starting point for thinking about data."

Advantages of immutable events

"When a transaction occurs, it is recorded in an append-only ledger, which is essentially a log of events describing money, goods, or services that have changed hands."

"The accounts, such as profit and loss or the balance sheet, are derived from the transactions in the ledger by adding them up."

"If a mistake is made, accountants don’t erase or change the incorrect transaction in the ledger — instead, they add another transaction that compensates for the mistake, for example refunding an incorrect charge."

"The incorrect transaction still remains in the ledger forever, because it might be important for auditing reasons."

"Although the second event cancels out the first event from the point of view of order fulfillment, it may be useful to know for analytics purposes that the customer was considering a particular item but then decided against it."

"This information is recorded in an event log, but would be lost in a database that deletes items when they are removed from the cart."

Deriving several views from the same event log

"Having an explicit translation step from an event log to a database makes it easier to evolve your application over time: if you want to introduce a new feature that presents your existing data in some new way, you can use the event log to build a separate read-optimized view for the new feature, and run it alongside the existing systems without having to modify them."

Concurrency control

"The biggest downside of event sourcing and change data capture is that the consumers of the event log are usually asynchronous, so there is a possibility that a user may make a write to the log, then read from a log-derived view and find that their write has not yet been reflected in the read view."

Limitations of immutability

"Some workloads mostly add data and rarely update or delete; they are easy to make immutable."

"Other workloads have a high rate of updates and deletes on a comparatively small dataset; in these cases, the immutable history may grow prohibitively large, fragmentation may become an issue, and the performance of compaction and garbage collection becomes crucial for operational robustness."

Processing Streams

"You can take the data in the events and write it to a database, cache, search index, or similar storage system, from where it can then be queried by other clients."

"You can push the events to users in some way, for example by sending email alerts or push notifications, or by streaming the events to a real-time dashboard where they are visualized."

"You can process one or more input streams to produce one or more output streams."

Uses of Stream Processing

"Trading systems need to examine price changes in a financial market and execute trades according to specified rules."

"Manufacturing systems need to monitor the status of machines in a factory, and quickly identify the problem if there is a malfunction"

Complex event processing

"CEP systems often use a high-level declarative query language like SQL, or a graphical user interface, to describe the patterns of events that should be detected."

"When a match is found, the engine emits a complex event (hence the name) with the details of the event pattern that was detected."

Stream analytics

"The boundary between CEP and stream analytics is blurry, but as a general rule, analytics tends to be less interested in finding specific event sequences and is more oriented toward aggregations and statistical metrics over a large number of events—for example:"

"Measuring the rate of some type of event (how often it occurs per time interval)"

"Probabilistic algorithms produce approximate results, but have the advantage of requiring significantly less memory in the stream processor than exact algorithms."

"Many open source distributed stream processing frameworks are designed with analytics in mind: for example, Apache Storm, Spark Streaming, Flink, Concord, Samza, and Kafka Streams."

"Hosted services include Google Cloud Dataflow and Azure Stream Analytics."

Maintaining materialized views

"Unlike stream analytics scenarios, it is usually not sufficient to consider only events within some time window: building the materialized view potentially requires all events over an arbitrary time period, apart from any obsolete events that may be discarded by log compaction."

"In effect, you need a window that stretches all the way back to the beginning of time."

Search on streams

"Conventional search engines first index the documents and then run queries over the index."

"By contrast, searching a stream turns the processing on its head: the queries are stored, and the documents run past the queries, like in CEP."

Message passing and RPC

"Actor frameworks are primarily a mechanism for managing concurrency and distributed execution of communicating modules, whereas stream processing is primarily a data management technique."

"Actors can communicate in arbitrary ways (including cyclic request/response patterns), but stream processors are usually set up in acyclic pipelines where every stream is the output of one particular job, and derived from a well-defined set of input streams."

Reasoning About Time

"A batch process may read a year’s worth of historical events within a few minutes; in most cases, the timeline of interest is the year of history, not the few minutes of processing."

"On the other hand, many stream processing frameworks use the local system clock on the processing machine (the processing time) to determine windowing."

Event time versus processing time

"If it helps to have an analogy, consider the Star Wars movies: Episode IV was released in 1977, Episode V in 1980, and Episode VI in 1983, followed by Episodes I, II, and III in 1999, 2002, and 2005, respectively, and Episode VII in 2015."

"As humans, we are able to cope with such discontinuities, but stream processing algorithms need to be specifically written to accommodate such timing and ordering issues."

Knowing when you’re ready

"A tricky problem when defining windows in terms of event time is that you can never be sure when you have received all of the events for a particular window, or whether there are some events still to come."

Whose clock are you using, anyway?

"Assigning timestamps to events is even more difficult when events can be buffered at several points in the system."

Types of windows

Tumbling window

"A tumbling window has a fixed length, and every event belongs to exactly one window."

"You could implement a 1-minute tumbling window by taking each event timestamp and rounding it down to the nearest minute to determine the window that it belongs to."

Hopping window

"For example, a 5-minute window with a hop size of 1 minute would contain the events between 10:03:00 and 10:07:59, then the next window would cover events between 10:04:00 and 10:08:59, and so on."

"You can implement this hopping window by first calculating 1-minute tumbling windows, and then aggregating over several adjacent windows."

Sliding window

"A sliding window contains all the events that occur within some interval of each other."

"A sliding window can be implemented by keeping a buffer of events sorted by time and removing old events when they expire from the window."

Session window

"Unlike the other window types, a session window has no fixed duration. Instead, it is defined by grouping together all events for the same user that occur closely together in time, and the window ends when the user has been inactive for some time (for example, if there have been no events for 30 minutes)."

Stream Joins

"Since stream processing generalizes data pipelines to incremental processing of unbounded datasets, there is exactly the same need for joins on streams."

Stream-stream join (window join)

"In order to calculate the click-through rate for each URL in the search results, you need to bring together the events for the search action and the click action, which are connected by having the same session ID."

"To implement this type of join, a stream processor needs to maintain state: for example, all the events that occurred in the last hour, indexed by session ID."

"Whenever a search event or click event occurs, it is added to the appropriate index, and the stream processor also checks the other index to see if another event for the same session ID has already arrived."

Stream-table join (stream enrichment)

"To perform this join, the stream process needs to look at one activity event at a time, look up the event’s user ID in the database, and add the profile information to the activity event."

"The difference to batch jobs is that a batch job uses a point-in-time snapshot of the database as input, whereas a stream processor is long-running, and the contents of the database are likely to change over time, so the stream processor’s local copy of the database needs to be kept up to date."

"This issue can be solved by change data capture: the stream processor can subscribe to a changelog of the user profile database as well as the stream of activity events."

Table-table join (materialized view maintenance)

"To implement this cache maintenance in a stream processor, you need streams of events for tweets (sending and deleting) and for follow relationships (following and unfollowing)."

Time-dependence of joins

"If the ordering of events across streams is undetermined, the join becomes nondeterministic, which means you cannot rerun the same job on the same input and necessarily get the same result: the events on the input streams may be interleaved in a different way when you run the job again."

"In data warehouses, this issue is known as a slowly changing dimension (SCD), and it is often addressed by using a unique identifier for a particular version of the joined record: for example, every time the tax rate changes, it is given a new identifier, and the invoice includes the identifier for the tax rate at the time of sale."

"This change makes the join deterministic, but has the consequence that log compaction is not possible, since all versions of the records in the table need to be retained."

Fault Tolerance

"In particular, the batch approach to fault tolerance ensures that the output of the batch job is the same as if nothing had gone wrong, even if in fact some tasks did fail."

"The same issue of fault tolerance arises in stream processing, but it is less straightforward to handle: waiting until a task is finished before making its output visible is not an option, because a stream is infinite and so you can never finish processing it."

Microbatching and checkpointing

"The batch size is typically around one second, which is the result of a performance compromise: smaller batches incur greater scheduling and coordination overhead, while larger batches mean a longer delay before results of the stream processor become visible."

Atomic commit revisited

"In order to give the appearance of exactly-once processing in the presence of faults, we need to ensure that all outputs and side effects of processing an event take effect if and only if the processing is successful."

Idempotence

"An idempotent operation is one that you can perform multiple times, and it has the same effect as if you performed it only once."

"For example, when consuming messages from Kafka, every message has a persistent, monotonically increasing offset."

"When writing a value to an external database, you can include the offset of the message that triggered the last write with the value."

"Thus, you can tell whether an update has already been applied, and avoid performing the same update again."

Rebuilding state after a failure

"In some cases, it may not even be necessary to replicate the state, because it can be rebuilt from the input streams."

Summary

AMQP/JMS-style message broker

"The broker assigns individual messages to consumers, and consumers acknowledge individual messages when they have been successfully processed."

Log-based message broker

"The broker assigns all messages in a partition to the same consumer node, and always delivers messages in the same order."

Stream-stream joins

"Both input streams consist of activity events, and the join operator searches for related events that occur within some window of time."

Stream-table joins

"One input stream consists of activity events, while the other is a database changelog."

Table-table joins

"In this case, every change on one side is joined with the latest state of the other side."

### 12 The Future of Data Systems

