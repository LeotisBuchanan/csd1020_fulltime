## Lab Exercise 2: Querying and Searching data in elasticsearch  

This lab exercise will introduce you to implementing elasticsearch queries.
The Elastic search queries are really comprehensive. The aim of this lab is not to cover all the queries,but to accomplish the following objectives
   
1. Perform basic queries on ingested data. 
2. Perform aggregation queries
3. Understand how to use Elasticsearch Query DSL
4. Understand how to use the elasticsearch reference documentation to learn     independently about other queries and features of elasticsearch

Lets get started. 

The reference guide can be found here: 

https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

For this lab, please enter all queries in the kibana dev console. 

Before starting please read the kibana documentation on using the devtools/dev console  here: https://www.elastic.co/guide/en/kibana/current/devtools-kibana.html

###  Match All Query

This will match all documents in the database
Can you think of and application for this query?

```json
GET events/_search
{
    "query": {
        "match_all": {}
    }
}
```

###  Match none Query
This is the inverse of the match_all query, which matches no documents.
Can you think of and application for this query.

```json
GET events/_search
{
    "query": {
        "match_none": {}
    }
}

```

###  Full text Queries

Full text queries are usually used for running full text queries on full text fields like the body of an email. 

## Match Query

```json
GET events/_search
{
    "query": {
        "match" : {
            "actor1geo_fullname" : "Washington, District of Columbia, United States"
        }
    }
}

```

You can also include an operator

```json
GET events/_search
{
    "query": {
        "match" : {
            "actor1geo_fullname" : {
            
                "query" : "Washington, District of Columbia, United States","operator" : "or"
            }
        }
    }
}

```

What does the operator do, what happens if you replace the "and" with "or"? 
(Hint look at the hits total)


## Multi Match Query

The multi_match query builds on the match query to allow multi-field queries:

```json

GET events/_search
{
  "query": {
    "multi_match" : {
      "query":    "AUS", 
      "fields": [ "actiongeo_adm1code", "actor1countrycode" ] 
    }
  }
}

```

Try changing the value for the query and make a note of its effect on the result. 


## Term Level Queries

Term-level queries operate on the exact terms

### Term Query

The term query finds documents that contain the exact term specified in the inverted index. For Example:

GET events/_search
{
  "query": {
    "term" : { "actor1geo_fullname.keyword" : "Bangladesh" } 
  }
}


### Terms Query

Retrieve documents that have fields that match any of the provided terms For example:

```json

GET events/_search
{
    "query": {
        "terms" : { "actor1geo_fullname.keyword" : ["Bangladesh", "Ghana"]}
    }
}

```

### Range Query

Matches documents with fields that have terms within a certain range.

```json

GET events/_search
{
    "query": {
        "range" : {
            "goldsteinscale" : {
                "gte" : 5,
                "lte" : 10,
                
            }
        }
    }
}
```

## Compound Queries

These queries allow you to combine multiple queries together.

### Bool Query

A query that matches documents matching boolean combinations of other queries.

Example:

```json

GET events/_search
{
  "query": {
    "bool" : {
      "must" : {
        "term" : { "actor1countrycode.keyword" : "BGD" }
      },
      "filter": {
        "term" : { "actor1geo_fullname.keyword" : "Bangladesh" }
      },
      "must_not" : {
        "range" : {
          "goldsteinscale" : { "gte" : -10, "lte" : 5 }
        }
      }
      
    }
  }
}
```

1. "must" clause must appear in matching documents and will contribute to the score.

2. "filter" clause must appear in matching documents. 

3. "should" clause appear in the matching document. I

4. "must_not" clause must not appear in the matching documents. 


## Aggregations Queries

The aggregations framework helps provide aggregated data based on a search query. 


### Avg aggregations

A single-value aggregation that computes the average of numeric values that are extracted from the aggregated documents.

```json

GET /events/_search?size=0
{
    "aggs" : {
        "avg_numsources" : { "avg" : { "field" : "numsources" } }
    }
}
```

### Extended Stats Aggregation
A multi-value metrics aggregation that computes stats over numeric values extracted from the aggregated documents

```json
GET /events/_search
{
    "size": 0,
    "aggs" : {
        "numsources_stats" : { "extended_stats" : { "field" : "numsources" } }
    }
}
```

We have covered the core queries, take the time to explore the other queries in elasticsearch reference document. Here is the link to the document. 
https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html 


## Tomorrows Class: Data Visualization and Analysis with Kibana. 