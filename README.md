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

