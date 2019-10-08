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

