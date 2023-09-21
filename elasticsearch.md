# ElasticSearch
Elasticsearch is a real-time distributed and open source full-text search and analytics engine.
Elasticsearch is an Apache Lucene-based search server, it manages load balancing and horizontal scaling of Apache lucene for data search.

> - Painless language is a Groovy (based on java and all of it's libraries are available to it) based scripting language that elasticsearch developers has built to secure and simplify work with elastic.

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
