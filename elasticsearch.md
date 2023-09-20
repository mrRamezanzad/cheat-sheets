# ElasticSearch
Elasticsearch is a real-time distributed and open source full-text search and analytics engine.
Elasticsearch is an Apache Lucene-based search server, it manages load balancing and horizontal scaling of Apache lucene for data search.

> - Painless language is a Groovy(based on java and all of it's libraries are available to it) based scripting language that elasticsearch developers has built to secure and simplify work with elastic.

## Apache lucene: 
Is a search enginge open-source software library. 
Searches by reverse indexing method which is mountains of data it split them into smaller mountains and thus it gets faster.
downside of this method is when the request goes up, vertical scaling (adding storage, Ram & CPU) will cost a lot and thus we need to scale horizontally and have a load balancer manage the request to different apache instances (this is the work elasticsearch does for us).

> - Elasticsearch uses lucene quey syntax for it's own queries.  

## Key Concepts
- Node:    
It refers to a single running instance of Elasticsearch. Single physical and virtual server accommodates multiple nodes depending upon the capabilities of their physical resources like RAM, storage and processing power.
Each node can have multiple replicas in order to distribute the data between them and lighten the search load. (data in each shard is partial of whole indexes and each of them without each other is incomplete)

- Cluster:   
It is a collection of one or more nodes. Cluster provides collective indexing and search capabilities across all the nodes for entire data (so they communicate with each otehr).

-  Index:  
It is a collection of different type of documents and their properties. Index also uses the concept of shards to improve the performance. For example, a set of document contains data of a social networking application.
It's alternative of table.

- Field  
Each column of rows is called a 'Field'.

- Document  
It is a collection of fields in a specific manner defined in JSON format. Every document belongs to a type and resides inside an index. Every document is associated with a unique identifier called the UID.
Each row in index is a 'document'.

- Shards:  
Indexes are horizontally subdivided into shards. This means each shard contains all the properties of document but contains less number of JSON objects than index. The horizontal separation makes shard an independent node, which can be store in any node. Primary shard is the original horizontal part of an index and then these primary shards are replicated into replica shards.
Distributed data of indexes over multiple Apache lucene instanes. 

- Replicas:
Elasticsearch allows a user to create replicas of their indexes and shards. Replication not only helps in increasing the availability of data in case of failure, but also improves the performance of searching by carrying out a parallel search operation in these replicas.


There are two type of shards:  

1 - Primary Shard:  
Data will get distributed on different shards in order to search faster, thus when a shard goes down and we loose a part of our search results.
Data is first stored in primary shards.

2 - Replica Shard:  
A replica shard is a copy of a primary shard (for down times). which should never be on same server as primary shard.
Data will be written here after primary shard.

## Elasticsearch Disadvantages
- Elasticsearch does not have multi-language support in terms of handling request and response data (only possible in JSON) unlike in Apache Solr, where it is possible in CSV, XML and JSON formats.

- Occasionally, Elasticsearch has a problem of Split brain situations.
    > Split brain is a state of a server cluster where nodes diverge from each other and have conflicts when handling incoming I/O operations. The servers may record the same data inconsistently or compete for resources.

## Elastic Search Commands

