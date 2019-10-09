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

