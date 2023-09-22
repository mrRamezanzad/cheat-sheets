# ElasticSearch
Elasticsearch is a real-time distributed and open source full-text search and analytics engine.
Elasticsearch is an Apache Lucene-based search server, it manages load balancing and horizontal scaling of Apache lucene for data search.

## Grok
 Logstash is the “L” in the ELK Stack (E: elastic, L: logstash, K: kibana) — the world's most popular log analysis platform and is responsible for aggregating data from different sources, processing it, and sending it down the pipeline, usually to be directly indexed in Elasticsearch.  

 Learn more about Grok : [tutorial](https://coralogix.com/blog/logstash-grok-tutorial-with-examples/)

**Grok** is extended defined pattern based on regex that has lots of predefined templates of regex in logstash documentation to extract and convert logs from data streams.  
e.g.   
`%{NUMBER:duration} %{IP:client} %{EMAILADDRESS: client_email}`

Let’s analyze how we would use Grok. Consider the following line in a log file:


```
2020-07-16T19:20:30.45+01:00 DEBUG This is a sample log
```

here is a grok pattern to extract it:

```json   
%{TIMESTAMP_ISO8601:time} %{LOGLEVEL:logLevel} %{GREEDYDATA:logMessage}`

// output: 
{
  "time": "2020-07-16T19:20:30.45+01:00",
  "logLevel": "DEBUG",
  "logMessage": "This is a sample log"
}
```

## Painless
Painless language is a Groovy (based on java and all of it's libraries are available to it) based scripting language that elasticsearch developers has built to secure and simplify work with elastic.

## Apache lucene: 
Is a search enginge open-source software library. 
Searches by reverse indexing method which is mountains of data it split them into smaller mountains and thus it gets faster.
downside of this method is when the request goes up, vertical scaling (adding storage, Ram & CPU) will cost a lot and thus we need to scale horizontally and have a load balancer manage the request to different apache instances (this is the work elasticsearch does for us).

> - Elasticsearch uses lucene query syntax for it's own queries.  

## Key Concepts
- Node:    
It refers to a single running instance of Elasticsearch. Single physical and virtual server accommodates multiple nodes depending upon the capabilities of their physical resources like RAM, storage and processing power.
Each node can have multiple replicas in order to distribute the data between them and lighten the search load. (data in each shard is partial of whole indexes and each of them without each other is incomplete)

- Cluster:   
It is a collection of one or more nodes. Cluster provides collective indexing and search capabilities across all the nodes for entire data (so they communicate with each otehr).

-  Index:  
It is a collection of different type of documents and their properties. Index also uses the concept of shards to improve the performance. For example, a set of document contains data of a social networking application.
It's alternative of table.
> Plural of Index is 'Indeces'

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

### Populate

- Create Index:   
```bash
PUT school  

// output: {"acknowledged": true}
```

- Add data:  
```bash
POST school/_doc/10
{
"name":"Saint Paul School", "description":"ICSE Afiliation",
"street":"Dawarka", "city":"Delhi", "state":"Delhi", "zip":"110075",
"location":[28.5733056, 77.0122136], "fees":5000,
"tags":["Good Faculty", "Great Sports"], "rating":"4.5"
}

// output: 
{{
   "_index" : "school",
   "_type" : "_doc",
   "_id" : "10",
   "_version" : 1,
   "result" : "created",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 2,
   "_primary_term" : 1
}
```

> Important Upgrading Note.   
Upgrade the components of your Elastic Stack in the following order: 
- Elasticsearch
- Kibana
- Logstash
- Beats
- APM Server

> If you use rolling upgrade which is for new versions, all services will be available and not down (but you always have to backup your data)

## API Conventions

### Comma Separated Notation
```bash
POST /index1,index2,index3/_search
{
   "query":{
      "query_string":{
         "query":"any_string"
      }
   }
}

// output: JSON objects from index1, index2, index3 having any_string in it.
```

### _all Keyword for All Indices
```bash
POST /_all/_search
{
   "query":{
      "query_string":{
         "query":"any_string"
      }
   }
}

// output: JSON objects from all indices and having any_string in it.
```

### Wildcards ( * , + , –)
- e.g.1

```bash 
POST /school*/_search
{
   "query":{
      "query_string":{
         "query":"CBSE"
      }
   }
}

// output: JSON objects from all indices which start with school having CBSE in it.
```

- e.g.2

```bash
POST /school*,-schools_gov /_search
{
   "query":{
      "query_string":{
         "query":"CBSE"
      }
   }
}

// output: JSON objects from all indices which start with “school” but not from schools_gov and having CBSE in it.
```

### ignore_unavailable − No error will occur or no operation will be stopped, if the one or more index(es) present in the URL does not exist. For example, schools index exists, but book_shops does not exist.
When books_shop is invalid index this happens.

```bash
POST /school*,book_shops/_search
{
   "query":{
      "query_string":{
         "query":"CBSE"
      }
   }
}

// output: {
   "error":{
      "root_cause":[{
         "type":"index_not_found_exception", "reason":"no such index",
         "resource.type":"index_or_alias", "resource.id":"book_shops",
         "index":"book_shops"
      }],
      "type":"index_not_found_exception", "reason":"no such index",
      "resource.type":"index_or_alias", "resource.id":"book_shops",
      "index":"book_shops"
   },"status":404
}
```

we can ignore it using ignore_unavailable:

```bash
POST /school*,book_shops/_search?ignore_unavailable = true
{
   "query":{
      "query_string":{
         "query":"CBSE"
      }
   }
}

// output: JSON objects from all indices which start with school having CBSE in it.
```

### allow_no_indices
true value of this parameter will prevent error, if a URL with wildcard results in no indices. For example, there is no index that starts with schools_pri −

```bash
POST /schools_pri*/_search?allow_no_indices=true
{
   "query":{
      "match_all":{}
   }
}

// output: {
   "took":1,"timed_out": false, "_shards":{"total":0, "successful":0, "failed":0},
   "hits":{"total":0, "max_score":0.0, "hits":[]}
}
```

### expand_wildcards
This parameter decides whether the wildcards need to be expanded to open indices or closed indices or perform both. The value of this parameter can be open and closed or none and all.

- e.g. For example, close index schools −
```bash
POST /schools/_close // output: {"acknowledged":true}
```

- Consider the following code −
```bash
POST /school*/_search?expand_wildcards = closed
{
   "query":{
      "match_all":{}
   }
}

// output: {
   "error":{
      "root_cause":[{
         "type":"index_closed_exception", "reason":"closed", "index":"schools"
      }],
      "type":"index_closed_exception", "reason":"closed", "index":"schools"
   }, "status":403
}
```

### Pretty Results
We can get response in a well-formatted JSON object by just appending a URL query parameter, i.e., pretty = true.

```bash
POST /schools/_search?pretty = true
{
   "query":{
      "match_all":{}
   }
}

// output: 
……………………..
{
   "_index" : "schools", "_type" : "school", "_id" : "1", "_score" : 1.0,
   "_source":{
      "name":"Central School", "description":"CBSE Affiliation",
      "street":"Nagan", "city":"paprola", "state":"HP", "zip":"176115",
      "location": [31.8955385, 76.8380405], "fees":2000,
      "tags":["Senior Secondary", "beautiful campus"], "rating":"3.5"
   }
}
………………….
```

### Human Readable Output
This option can change the statistical responses either into human readable form (If human = true) or computer readable form (if human = false). For example, if human = true then distance_kilometer = 20KM and if human = false then distance_meter = 20000, when response needs to be used by another computer program.

### Response Filtering
We can filter the response to less fields by adding them in the field_path parameter. For example,

```bash
POST /schools/_search?filter_path = hits.total
Request Body
{
   "query":{
      "match_all":{}
   }
}

// output: {"hits":{"total":3}}
```

### get all Indices (indexes)
```bash 
GET _cat/indices
```

### Deleta Index
```bash
DELETE posts
```


### Reindex
```bash
POST _reindex
{
  "source": {
    "index": "posts"
  },
  "dest": {
    "index": "article"
  }
}
```

## Elasticsearch - Document APIs

### Index API
It helps to add or update the JSON document in an index when a request is made to that respective index with specific mapping. For example, the following request will add the JSON object to index schools and under school mapping −

```bash
PUT schools/_doc/5
{
   "name":"City School", "description":"ICSE", "street":"West End",
   "city":"Meerut",
   "state":"UP", "zip":"250002", "location":[28.9926174, 77.692485],
   "fees":3500,
   "tags":["fully computerized"], "rating":"4.5"
}

// output: {
   "_index" : "schools",
   "_type" : "_doc",
   "_id" : "5",
   "_version" : 1,
   "result" : "created",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 2,
   "_primary_term" : 1
}
```

### Automatic Index Creation
When a request is made to add JSON object to a particular index and if that index does not exist, then this API automatically creates that index and also the underlying mapping for that particular JSON object. This functionality can be disabled by changing the values of following parameters to false, which are present in elasticsearch.yml file.
`
action.auto_create_index:false
index.mapper.dynamic:false
`  

You can also restrict the auto creation of index, where only index name with specific patterns are allowed by changing the value of the following parameter −   
`
action.auto_create_index:+acc*,-bank*
`
> Note − Here + indicates allowed and – indicates not allowed.

### Versioning
Elasticsearch also provides version control facility. We can use a version query parameter to specify the version of a particular document.

```bash 
PUT schools/_doc/5?version=7&version_type=external
{
   "name":"Central School", "description":"CBSE Affiliation", "street":"Nagan",
   "city":"paprola", "state":"HP", "zip":"176115", "location":[31.8955385, 76.8380405],
   "fees":2200, "tags":["Senior Secondary", "beautiful campus"], "rating":"3.3"
}

// output: {
   "_index" : "schools",
   "_type" : "_doc",
   "_id" : "5",
   "_version" : 7,
   "result" : "updated",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 3,
   "_primary_term" : 1
}
```
> Versioning is a real-time process and it is not affected by the real time search operations.

There are two most important types of versioning:

- Internal Versioning
Internal versioning is the default version that starts with 1 and increments with each update, deletes included.

- External Versioning
It is used when the versioning of the documents is stored in an external system like third party versioning systems. To enable this functionality, we need to set version_type to external. Here Elasticsearch will store version number as designated by the external system and will not increment them automatically.

### Operation Type
The operation type is used to force a create operation. This helps to avoid the overwriting of existing document.
> It will throw error if document already exists.

```bash
PUT chapter/_doc/1?op_type=create
{
   "Text":"this is chapter one"
}
// output: {
   "_index" : "chapter",
   "_type" : "_doc",
   "_id" : "1",
   "_version" : 1,
   "result" : "created",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 0,
   "_primary_term" : 1
}
```

### Automatic ID generation
When ID is not specified in index operation, then Elasticsearch automatically generates id for that document.

```bash
POST chapter/_doc/
{
   "user" : "tpoint",
   "post_date" : "2018-12-25T14:12:12",
   "message" : "Elasticsearch Tutorial"
}

// output: {
   "_index" : "chapter",
   "_type" : "_doc",
   "_id" : "PVghWGoB7LiDTeV6LSGu",
   "_version" : 1,
   "result" : "created",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 1,
   "_primary_term" : 1
}
```

### Get API
API helps to extract type JSON object by performing a get request for a particular document.

```bash 
GET schools/_doc/5

// output: {
   "_index" : "schools",
   "_type" : "_doc",
   "_id" : "5",
   "_version" : 7,
   "_seq_no" : 3,
   "_primary_term" : 1,
   "found" : true,
   "_source" : {
      "name" : "Central School",
      "description" : "CBSE Affiliation",
      "street" : "Nagan",
      "city" : "paprola",
      "state" : "HP",
      "zip" : "176115",
      "location" : [
         31.8955385,
         76.8380405
      ],
      "fees" : 2200,
      "tags" : [
         "Senior Secondary",
         "beautiful campus"
      ],
      "rating" : "3.3"
   }
}
```

> - This operation is real time and does not get affected by the refresh rate of Index.
> - You can also specify the version, then Elasticsearch will fetch that version of document only.
> - You can also specify the _all in the request, so that the Elasticsearch can search for that document id in every type and it will return the first matched document.
> - You can also specify the fields you want in your result from that particular document.

```bash
GET schools/_doc/5?_source_includes=name,fees 

// output:
 {
   "_index" : "schools",
   "_type" : "_doc",
   "_id" : "5",
   "_version" : 7,
   "_seq_no" : 3,
   "_primary_term" : 1,
   "found" : true,
   "_source" : {
      "fees" : 2200,
      "name" : "Central School"
   }
} 
```

> You can also fetch the source part in your result by just adding _source part in your get request.
`GET schools/_doc/5?_source `

> You can also refresh the shard before doing get operation by set refresh parameter to true.

### Delete API
You can delete a particular index, mapping or a document by sending a HTTP DELETE request to Elasticsearch.

```bash 
DELETE schools/_doc/4  

// output: {
   "found":true, "_index":"schools", "_type":"school", "_id":"4", "_version":2,
   "_shards":{"total":2, "successful":1, "failed":0}
}
```

> Version of the document can be specified to delete that particular version. Routing parameter can be specified to delete the document from a particular user and the operation fails if the document does not belong to that particular user. In this operation, you can specify refresh and timeout option same like GET API.

### Update API
Script is used for performing this operation and versioning is used to make sure that no updates have happened during the get and re-index. For example, you can update the fees of school using script −

```bash
POST schools/_update/4
{
   "script" : {
      "source": "ctx._source.name = params.sname",
      "lang": "painless",
      "params" : {
         "sname" : "City Wise School"
      }
   }
 }

// output: {
   "_index" : "schools",
   "_type" : "_doc",
   "_id" : "4",
   "_version" : 3,
   "result" : "updated",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 4,
   "_primary_term" : 2
}
```

## Elasticsearch - Search APIs
This API is used to search content in Elasticsearch. A user can search by sending a get request with query string as a parameter or they can post a query in the message body of post request. Mainly all the search APIS are multi-index, multi-type.

### Multi-Index
Elasticsearch allows us to search for the documents present in all the indices or in some specific indices. For example, if we need to search all the documents with a name that contains central, we can do as shown here −

```bash 
GET /_all/_search?q=city:paprola 

// output: {
   "took" : 33,
   "timed_out" : false,
   "_shards" : {
      "total" : 7,
      "successful" : 7,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 1,
         "relation" : "eq"
      },
      "max_score" : 0.9808292,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "5",
            "_score" : 0.9808292,
            "_source" : {
               "name" : "Central School",
               "description" : "CBSE Affiliation",
               "street" : "Nagan",
               "city" : "paprola",
               "state" : "HP",
               "zip" : "176115",
               "location" : [
                  31.8955385,
                  76.8380405
               ],
               "fees" : 2200,
               "tags" : [
                  "Senior Secondary",
                  "beautiful campus"
               ],
               "rating" : "3.3"
            }
         }
      ]
   }
}
```

### URI Search
Many parameters can be passed in a search operation using Uniform Resource Identifier 

- **q**: This parameter is used to specify query string.
- **lenient**: This parameter is used to specify query string.Format based errors can be ignored by just setting this parameter to true. It is false by default.
- **fields**: This parameter is used to specify query string.
- **sort**: We can get sorted result by using this parameter, the possible values for this parameter is fieldName, fieldName:asc/fieldname:desc
- **timeout**: We can restrict the search time by using this parameter and response only contains the hits in that specified time. By default, there is no timeout.
- **terminate_after**: We can restrict the response to a specified number of documents for each shard, upon reaching which the query will terminate early. By default, there is no terminate_after.
- **from**: The starting from index of the hits to return. Defaults to 0.
- **size**: It denotes the number of hits to return. Defaults to 10.

### Request Body Search
We can also specify query using query DSL in request body and there are many examples already given in previous chapters. One such example is given here −

```bash
POST /schools/_search
{
   "query":{
      "query_string":{
         "query":"up"
      }
   }
}

// output: {
   "took" : 11,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 1,
         "relation" : "eq"
      },
      "max_score" : 0.47000363,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "4",
            "_score" : 0.47000363,
            "_source" : {
               "name" : "City Best School",
               "description" : "ICSE",
               "street" : "West End",
               "city" : "Meerut",
               "state" : "UP",
               "zip" : "250002",
               "location" : [
                  28.9926174,
                  77.692485
               ],
               "fees" : 3500,
               "tags" : [
                  "fully computerized"
               ],
               "rating" : "4.5"
            }
         }
      ]
   }
}
```

## Aggregations
The aggregations framework collects all the data selected by the search query and consists of many building blocks, which help in building complex summaries of the data. The basic structure of an aggregation is shown here 
```bash
"aggregations" : {
   "" : {
      "" : {

      }
 
      [,"meta" : { [] } ]?
      [,"aggregations" : { []+ } ]?
   }
   [,"" : { ... } ]*
}
```

## Metrics Aggregations:
These aggregations help in computing matrices from the field’s values of the aggregated documents and sometime some values can be generated from scripts.

Numeric matrices are either single-valued like average aggregation or multi-valued like stats.

### Avg Aggregation: 
This aggregation is used to get the average of any numeric field present in the aggregated documents. For example,

```bash
POST /schools/_search
{
   "aggs":{
      "avg_fees":{"avg":{"field":"fees"}}
   }
}

// output: {
   "took" : 41,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "5",
            "_score" : 1.0,
            "_source" : {
               "name" : "Central School",
               "description" : "CBSE Affiliation",
               "street" : "Nagan",
               "city" : "paprola",
               "state" : "HP",
               "zip" : "176115",
               "location" : [
                  31.8955385,
                  76.8380405
               ],
            "fees" : 2200,
            "tags" : [
               "Senior Secondary",
               "beautiful campus"
            ],
            "rating" : "3.3"
         }
      },
      {
         "_index" : "schools",
         "_type" : "school",
         "_id" : "4",
         "_score" : 1.0,
         "_source" : {
            "name" : "City Best School",
            "description" : "ICSE",
            "street" : "West End",
            "city" : "Meerut",
            "state" : "UP",
            "zip" : "250002",
            "location" : [
               28.9926174,
               77.692485
            ],
            "fees" : 3500,
            "tags" : [
               "fully computerized"
            ],
            "rating" : "4.5"
         }
      }
   ]
 },
   "aggregations" : {
      "avg_fees" : {
         "value" : 2850.0
      }
   }
}
```
### Cardinality Aggregation
This aggregation gives the count of distinct values of a particular field.

```bash
# set size to zero in order to prevent document retriving
POST /schools/_search?size=0 
{
   "aggs":{
      "distinct_name_count":{"cardinality":{"field":"fees"}}
   }
}

// output: {
   "took" : 2,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "distinct_name_count" : {
         "value" : 2
      }
   }
}
```
### Extended Stats Aggregation
This aggregation generates all the statistics about a specific numerical field in aggregated documents.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
      "fees_stats" : { "extended_stats" : { "field" : "fees" } }
   }
}

// output: {
   "took" : 8,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "fees_stats" : {
         "count" : 2,
         "min" : 2200.0,
         "max" : 3500.0,
         "avg" : 2850.0,
         "sum" : 5700.0,
         "sum_of_squares" : 1.709E7,
         "variance" : 422500.0,
         "std_deviation" : 650.0,
         "std_deviation_bounds" : {
            "upper" : 4150.0,
            "lower" : 1550.0
         }
      }
   }
}
```

### Max Aggregation
This aggregation finds the max value of a specific numeric field in aggregated documents.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
   "max_fees" : { "max" : { "field" : "fees" } }
   }
}

// output: {
   "took" : 16,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
  "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "max_fees" : {
         "value" : 3500.0
      }
   }
}
```

### Min Aggregation
This aggregation finds the min value of a specific numeric field in aggregated documents.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
      "min_fees" : { "min" : { "field" : "fees" } }
   }
}

// output: {
   "took" : 2,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
  "aggregations" : {
      "min_fees" : {
         "value" : 2200.0
      }
   }
}
```

### Sum Aggregation
This aggregation calculates the sum of a specific numeric field in aggregated documents.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
      "total_fees" : { "sum" : { "field" : "fees" } }
   }
}

// output: {
   "took" : 8,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "total_fees" : {
         "value" : 5700.0
      }
   }
}
```

> **Important: There are some other metrics aggregations which are used in special cases like geo bounds aggregation and geo centroid aggregation for the purpose of geo location.**

### Stats Aggregations
A multi-value metrics aggregation that computes stats over numeric values extracted from the aggregated documents.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
      "grades_stats" : { "stats" : { "field" : "fees" } }
   }
}

// output: {
   "took" : 2,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "grades_stats" : {
         "count" : 2,
         "min" : 2200.0,
         "max" : 3500.0,
         "avg" : 2850.0,
         "sum" : 5700.0
      }
   }
}
```

### Aggregation Metadata
You can add some data about the aggregation at the time of request by using meta tag and can get that in response.

```bash
POST /schools/_search?size=0
{
   "aggs" : {
      "avg_fees" : { 
        "avg" : { "field" : "fees" } ,
        "meta" :{"dsc" :"Lowest Fees This Year"}
      }
   }
}

// output: {
   "took" : 0,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   },
   "aggregations" : {
      "avg_fees" : {
         "meta" : {
            "dsc" : "Lowest Fees This Year"
         },
         "value" : 2850.0
      }
   }
}
```

## Index APIs
These APIs are responsible for managing all the aspects of the index like settings, aliases, mappings, index templates.

### Create Index
This API helps you to create an index. An index can be created automatically when a user is passing JSON objects to any index or it can be created before that. To create an index, you just need to send a PUT request with settings, mappings and aliases or just a simple request without body.

```bash
PUT colleges

// output: {
   "acknowledged" : true,
   "shards_acknowledged" : true,
   "index" : "colleges"
}
```

with more commands it's like:

```bash
PUT colleges
{
  "settings" : {
      "index" : {
         "number_of_shards" : 3,
         "number_of_replicas" : 2
      }
   }
}

// output: {
    "acknowledged" : true,
    "shards_acknowledged" : true,
    "index" : "colleges"
}
```

### Delete Index
This API helps you to delete any index.

```bash
DELETE /colleges
```

> You can delete all indices by just using `_all` or `*`

### Get Index
Returns the information about index.

```bash
GET colleges

// output: {
   "colleges" : {
      "aliases" : {
         "alias_1" : { },
         "alias_2" : {
            "filter" : {
               "term" : {
                  "user" : "pkay"
               }
            },
            "index_routing" : "pkay",
            "search_routing" : "pkay"
         }
      },
      "mappings" : { },
      "settings" : {
         "index" : {
            "creation_date" : "1556245406616",
            "number_of_shards" : "1",
            "number_of_replicas" : "1",
            "uuid" : "3ExJbdl2R1qDLssIkwDAug",
            "version" : {
               "created" : "7000099"
            },
            "provided_name" : "colleges"
         }
      }
   }
}
```
> You can get the information of all the indices by using `_all` or `*`

### Index Exist
Existence of an index can be determined by just sending a get request to that index. If the HTTP response is 200, it exists; if it is 404, it does not exist.

```bash
HEAD colleges

// ouput: 200-OK
```

### Index Settings
You can get the index settings by just appending _settings keyword at the end of URL.

```bash
GET /colleges/_settings

// ouput: {
   "colleges" : {
      "settings" : {
         "index" : {
            "creation_date" : "1556245406616",
            "number_of_shards" : "1",
            "number_of_replicas" : "1",
            "uuid" : "3ExJbdl2R1qDLssIkwDAug",
            "version" : {
               "created" : "7000099"
            },
            "provided_name" : "colleges"
         }
      }
   }
}
```

### Index Stats
Extract statistics about a particular index. You just need to send a get request with the index URL and _stats keyword at the end.

```bash
GET /_stats

// output: ………………………………………………
},
   "request_cache" : {
      "memory_size_in_bytes" : 849,
      "evictions" : 0,
      "hit_count" : 1171,
      "miss_count" : 4
   },
   "recovery" : {
      "current_as_source" : 0,
      "current_as_target" : 0,
      "throttle_time_in_millis" : 0
   }
} ………………………………………………
```

### Flush
The flush process of an index makes sure that any data that is currently only persisted in the transaction log is also permanently persisted in Lucene. This reduces recovery times as that data does not need to be reindexed from the transaction logs after the Lucene indexed is opened.

```bash
POST colleges/_flush

// output: {
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   } 
}
```

## Cat APIs
Usually the results from various Elasticsearch APIs are displayed in JSON format. But JSON is not easy to read always. So cat APIs feature is available in Elasticsearch helps in taking care of giving an easier to read and comprehend printing format of the results. There are various parameters used in cat API which server different purpose, for example - the term V makes the output verbose.

### Verbose
The verbose output gives a nice display of results of a cat command.

```bash
GET /_cat/indices?v

output:

health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
yellow open schools RkMyEn2SQ4yUgzT6EQYuAA 1 1 2 1 21.6kb 21.6kb
yellow open index_4_analysis zVmZdM1sTV61YJYrNXf1gg 1 1 0 0 283b 283b
yellow open sensor-2018-01-01 KIrrHwABRB-ilGqTu3OaVQ 1 1 1 0 4.2kb 4.2kb
yellow open colleges 3ExJbdl2R1qDLssIkwDAug 1 1 0 0 283b 283b
```
### Headers
The h parameter, also called header, is used to display only those columns mentioned in the command.

```bash
GET /_cat/nodes?h=ip,port // output: 127.0.0.1 9300
```

### Sort
The sort command accepts query string which can sort the table by specified column in the query. The default sort is ascending but this can be changed by adding :desc to a column

The below example, gives a result of templates arranged in descending order of the filed index patterns.

```bash
GET _cat/templates?v&s=order:desc,index_patterns

output: 

name index_patterns order version
.triggered_watches [.triggered_watches*] 2147483647
.watch-history-9 [.watcher-history-9*] 2147483647
.watches [.watches*] 2147483647
.kibana_task_manager [.kibana_task_manager] 0 7000099
```

### Count
The count parameter provides the count of total number of documents in the entire cluster.

```bash
GET /_cat/count?v

output: 
epoch timestamp count
1557633536 03:58:56 17809
```
> Epoch : a particular period of time in history or a person's life.  [عصر،‌ دوره] 
One Epoch is completed when a complete dataset is cycled forward and backward through the neural network or you can say your neural network has watched the entire dataset for once.

## Cluster APIs
The cluster API is used for getting information about cluster and its nodes and to make changes in them. To call this API, we need to specify the node name, address or _local.

```bash
GET /_nodes/_local

output: 
………………………………………………
cluster_name" : "elasticsearch",
   "nodes" : {
      "FKH-5blYTJmff2rJ_lQOCg" : {
         "name" : "ubuntu",
         "transport_address" : "127.0.0.1:9300",
         "host" : "127.0.0.1",
         "ip" : "127.0.0.1",
         "version" : "7.0.0",
         "build_flavor" : "default",
         "build_type" : "tar",
         "build_hash" : "b7e28a7",
         "total_indexing_buffer" : 106502553,
         "roles" : [
            "master",
            "data",
            "ingest"
         ],
         "attributes" : {
………………………………………………
```

### Cluster Health
This API is used to get the status on the health of the cluster by appending the ‘health’ keyword.

```bash
GET /_cluster/health

output:
{
   "cluster_name" : "elasticsearch",
   "status" : "yellow",
   "timed_out" : false,
   "number_of_nodes" : 1,
   "number_of_data_nodes" : 1,
   "active_primary_shards" : 7,
   "active_shards" : 7,
   "relocating_shards" : 0,
   "initializing_shards" : 0,
   "unassigned_shards" : 4,
   "delayed_unassigned_shards" : 0,
   "number_of_pending_tasks" : 0,
   "number_of_in_flight_fetch" : 0,
   "task_max_waiting_in_queue_millis" : 0,
   "active_shards_percent_as_number" : 63.63636363636363
}
```

### Cluster State
This API is used to get state information about a cluster by appending the ‘state’ keyword URL. The state information contains version, master node, other nodes, routing table, metadata and blocks.

```bash
GET /_cluster/state

output: 
………………………………………………
{
   "cluster_name" : "elasticsearch",
   "cluster_uuid" : "IzKu0OoVTQ6LxqONJnN2eQ",
   "version" : 89,
   "state_uuid" : "y3BlwvspR1eUQBTo0aBjig",
   "master_node" : "FKH-5blYTJmff2rJ_lQOCg",
   "blocks" : { },
   "nodes" : {
      "FKH-5blYTJmff2rJ_lQOCg" : {
      "name" : "ubuntu",
      "ephemeral_id" : "426kTGpITGixhEzaM-5Qyg",
      "transport
   }
}
………………………………………………
```

### Cluster Stats
This API helps to retrieve statistics about cluster by using the ‘stats’ keyword. This API returns shard number, store size, memory usage, number of nodes, roles, OS, and file system.

```bash
GET /_cluster/stats

output: 
………………………………………….
"cluster_name" : "elasticsearch",
"cluster_uuid" : "IzKu0OoVTQ6LxqONJnN2eQ",
"timestamp" : 1556435464704,
"status" : "yellow",
"indices" : {
   "count" : 7,
   "shards" : {
      "total" : 7,
      "primaries" : 7,
      "replication" : 0.0,
      "index" : {
         "shards" : {
         "min" : 1,
         "max" : 1,
         "avg" : 1.0
      },
      "primaries" : {
         "min" : 1,
         "max" : 1,
         "avg" : 1.0
      },
      "replication" : {
         "min" : 0.0,
         "max" : 0.0,
         "avg" : 0.0
      }
…………………………………………
```

### Cluster Update Settings
This API allows you to update the settings of a cluster by using the ‘settings’ keyword. There are two types of settings − persistent (applied across restarts) and transient (do not survive a full cluster restart).

### Node Stats
This API is used to retrieve the statistics of one more nodes of the cluster. Node stats are almost the same as cluster.

```bash
GET /_nodes/stats

output: 
{
   "_nodes" : {
      "total" : 1,
      "successful" : 1,
      "failed" : 0
   },
   "cluster_name" : "elasticsearch",
   "nodes" : {
      "FKH-5blYTJmff2rJ_lQOCg" : {
         "timestamp" : 1556437348653,
         "name" : "ubuntu",
         "transport_address" : "127.0.0.1:9300",
         "host" : "127.0.0.1",
         "ip" : "127.0.0.1:9300",
         "roles" : [
            "master",
            "data",
            "ingest"
         ],
         "attributes" : {
            "ml.machine_memory" : "4112797696",
            "xpack.installed" : "true",
            "ml.max_open_jobs" : "20"
         },
………………………………………………………….
```

### Nodes hot_threads
This API helps you to retrieve information about the current hot threads on each node in cluster.

```bash
GET /_nodes/hot_threads

output: 
:::{ubuntu}{FKH-5blYTJmff2rJ_lQOCg}{426kTGpITGixhEzaM5Qyg}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=4112797696,
xpack.installed=true, ml.max_open_jobs=20}
 Hot threads at 2019-04-28T07:43:58.265Z, interval=500ms, busiestThreads=3,
ignoreIdleThreads=true:
```
##  Query DSL
In Elasticsearch, searching is carried out by using query based on JSON. A query is made up of two clauses: 
- Leaf Query Clauses − These clauses are match, term or range, which look for a specific value in specific field.

- Compound Query Clauses − These queries are a combination of leaf query clauses and other compound queries to extract the desired information.

A query starts with a query key word and then has conditions and filters inside in the form of JSON object.


### Match All Query
This is the most basic query; it returns all the content and with the score of 1.0 for every object.

```bash
POST /schools/_search
{
   "query":{
      "match_all":{}
   }
}

output: 
{
   "took" : 7,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "5",
            "_score" : 1.0,
            "_source" : {
               "name" : "Central School",
               "description" : "CBSE Affiliation",
               "street" : "Nagan",
               "city" : "paprola",
               "state" : "HP",
               "zip" : "176115",
               "location" : [
                  31.8955385,
                  76.8380405
               ],
               "fees" : 2200,
               "tags" : [
                  "Senior Secondary",
                  "beautiful campus"
               ],
               "rating" : "3.3"
            }
         },
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "4",
            "_score" : 1.0,
            "_source" : {
               "name" : "City Best School",
               "description" : "ICSE",
               "street" : "West End",
               "city" : "Meerut",
               "state" : "UP",
               "zip" : "250002",
               "location" : [
                  28.9926174,
                  77.692485
               ],
               "fees" : 3500,
               "tags" : [
                  "fully computerized"
               ],
               "rating" : "4.5"
            }
         }
      ]
   }
}
```

### Full Text Queries
These queries are used to search a full body of text like a chapter or a news article. This query works according to the analyser associated with that particular index or document. 

#### Match query
This query matches a text or phrase with the values of one or more fields.

```bash
POST /schools*/_search
{
   "query":{
      "match" : {
         "rating":"4.5"
      }
   }
}

output: 
{
   "took" : 44,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 1,
         "relation" : "eq"
      },
      "max_score" : 0.47000363,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "4",
            "_score" : 0.47000363,
            "_source" : {
               "name" : "City Best School",
               "description" : "ICSE",
               "street" : "West End",
               "city" : "Meerut",
               "state" : "UP",
               "zip" : "250002",
               "location" : [
                  28.9926174,
                  77.692485
               ],
               "fees" : 3500,
               "tags" : [
                  "fully computerized"
               ],
               "rating" : "4.5"
            }
         }
      ]
   }
}
```

#### Multi Match Query
This query matches a text or phrase with more than one field.

```bash
POST /schools*/_search
{
   "query":{
      "multi_match" : {
         "query": "paprola",
         "fields": [ "city", "state" ]
      }
   }
}

output: 
{
   "took" : 12,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 1,
         "relation" : "eq"
      },
      "max_score" : 0.9808292,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "5",
            "_score" : 0.9808292,
            "_source" : {
               "name" : "Central School",
               "description" : "CBSE Affiliation",
               "street" : "Nagan",
               "city" : "paprola",
               "state" : "HP",
               "zip" : "176115",
               "location" : [
                  31.8955385,
                  76.8380405
               ],
               "fees" : 2200,
               "tags" : [
                  "Senior Secondary",
                  "beautiful campus"
               ],
               "rating" : "3.3"
            }
         }
      ]
   }
}
```

#### Query String Query
This query uses query parser and query_string keyword.

```bash
POST /schools*/_search
{
   "query":{
      "query_string":{
         "query":"beautiful"
      }
   }
}  

output:
{
   "took" : 60,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
      "value" : 1,
      "relation" : "eq"
   },
………………………………….
```

#### Term Level Queries
These queries mainly deal with structured data like numbers, dates and enums.

```bash
POST /schools*/_search
{
   "query":{
      "term":{"zip":"176115"}
   }
}

output:
……………………………..
hits" : [
   {
      "_index" : "schools",
      "_type" : "school",
      "_id" : "5",
      "_score" : 0.9808292,
      "_source" : {
         "name" : "Central School",
         "description" : "CBSE Affiliation",
         "street" : "Nagan",
         "city" : "paprola",
         "state" : "HP",
         "zip" : "176115",
         "location" : [
            31.8955385,
            76.8380405
         ],
      }
   }
]   
…………………………………………
```

#### Range Query
This query is used to find the objects having values between the ranges of values given. For this, we need to use operators such as −

- gte: greater than equal to  
- gt : greater-than  
- lte: less-than equal to  
- lt : less-than  

```bash
POST /schools*/_search
{
   "query":{
      "range":{
         "rating":{
            "gte":3.5
         }
      }
   }
}

output: 
{
   "took" : 24,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 1,
         "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
         {
            "_index" : "schools",
            "_type" : "school",
            "_id" : "4",
            "_score" : 1.0,
            "_source" : {
               "name" : "City Best School",
               "description" : "ICSE",
               "street" : "West End",
               "city" : "Meerut",
               "state" : "UP",
               "zip" : "250002",
               "location" : [
                  28.9926174,
                  77.692485
               ],
               "fees" : 3500,
               "tags" : [
                  "fully computerized"
               ],
               "rating" : "4.5"
            }
         }
      ]
   }
}
```

There exist other types of term level queries also such as

- Exists query − If a certain field has non null value.

- Missing query − This is completely opposite to exists query, this query searches for objects without specific fields or fields having null value.

- Wildcard or regexp query − This query uses regular expressions to find patterns in the objects.

#### Compound Queries
These queries are a collection of different queries merged with each other by using Boolean operators like and, or, not or for different indices or having function calls etc.

```bash
POST /schools/_search
{
   "query": {
      "bool" : {
         "must" : {
            "term" : { "state" : "UP" }
         },
         "filter": {
            "term" : { "fees" : "2200" }
         },
         "minimum_should_match" : 1,
         "boost" : 1.0
      }
   }
}

output: 
{
   "took" : 6,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 0,
         "relation" : "eq"
      },
      "max_score" : null,
      "hits" : [ ]
   }
}
```

#### Geo Queries
These queries deal with geo locations and geo points. These queries help to find out schools or any other geographical object near to any location. You need to use geo point data type.

```bash
PUT /geo_example
{
   "mappings": {
      "properties": {
         "location": {
            "type": "geo_shape"
         }
      }
   }
}

output: 
{  "acknowledged" : true,
   "shards_acknowledged" : true,
   "index" : "geo_example"
}
```

Now we post the data in the index created above.

```bash
POST /geo_example/_doc?refresh
{
   "name": "Chapter One, London, UK",
   "location": {
      "type": "point",
      "coordinates": [11.660544, 57.800286]
   }
}

output: 

   "took" : 1,
   "timed_out" : false,
   "_shards" : {
      "total" : 1,
      "successful" : 1,
      "skipped" : 0,
      "failed" : 0
   },
   "hits" : {
      "total" : {
         "value" : 2,
         "relation" : "eq"
      },
      "max_score" : 1.0,
      "hits" : [
         "_index" : "geo_example",
         "_type" : "_doc",
         "_id" : "hASWZ2oBbkdGzVfiXHKD",
         "_score" : 1.0,
         "_source" : {
            "name" : "Chapter One, London, UK",
            "location" : {
               "type" : "point",
               "coordinates" : [
                  11.660544,
                  57.800286
               ]
            }
         }
      }
   }
```

## Mapping
Mapping is the outline of the documents stored in an index. It defines the data type like geo_point or string and format of the fields present in the documents and rules to control the mapping of dynamically added fields.

```bash
PUT bankaccountdetails
{
   "mappings":{
      "properties":{
         "name": { "type":"text"},
         "date":{ "type":"date"},
         "balance":{ "type":"double"},
         "liability":{ "type":"double"}
      }
   }
 }
 
 output:
 {
   "acknowledged" : true,
   "shards_acknowledged" : true,
   "index" : "bankaccountdetails"
}
```
### Field Data Types
Elasticsearch supports a number of different datatypes for the fields in a document.

#### Core Data Types
These are the basic data types such as text, keyword, date, long, double, boolean or ip, which are supported by almost all the systems.

#### Complex Data Types
These data types are a combination of core data types. These include array, JSON object and nested data type. An example of nested data type is shown below &minus

e.g.1
```bash
POST /tabletennis/_doc/1
{
   "group" : "players",
   "user" : [
      {
         "first" : "dave", 
         "last" : "jones"
      },
      {
         "first" : "kevin", 
         "last" : "morris"
      }
   ]
}

output:
{
   "_index" : "tabletennis",
   "_type" : "_doc",
   "_id" : "1",
   _version" : 2,
   "result" : "updated",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 1,
   "_primary_term" : 1
}
```

e.g.2
```bash
POST /accountdetails/_doc/1
{
   "from_acc":"7056443341",
   "to_acc":"7032460534",
   "date":"11/1/2016",
   "amount":10000
}

output:
{  "_index" : "accountdetails",
   "_type" : "_doc",
   "_id" : "1",
   "_version" : 1,
   "result" : "created",
   "_shards" : {
      "total" : 2,
      "successful" : 1,
      "failed" : 0
   },
   "_seq_no" : 1,
   "_primary_term" : 1
}
```

We can check the above document by using the following command

```bash
GET /accountdetails/_mappings

output:
{
  "accountdetails": {
    "mappings": {
      "properties": {
        "amount": {
          "type": "long"
        },
        "date": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "from_acc": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "to_acc": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
      }
    }
  }
}
```

## Removal of Mapping Types
Indices created in Elasticsearch 7.0.0 or later no longer accept a _default_ mapping. Indices created in 6.x will continue to function as before in Elasticsearch 6.x. Types are deprecated in APIs in 7.0.
