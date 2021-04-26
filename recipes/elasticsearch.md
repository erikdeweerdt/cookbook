# ElasticSearch

Although you can use cURL to query the cluster, a tool like [cerebro](https://github.com/lmenezes/cerebro) is more user friendly.

## Cluster maintenance

[Get information about shard allocation](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-allocation-explain.html), including the reasons for any failures:
`GET _cluster/allocation/explain`
```json
{
    "index": "my-index",
    "shard": 0,
    "primary": false
}
```
(note that `primary=false` requests information about replica shards)

Retry allocating shards that exceeded their maximum tries:
`POST _cluster/reroute?retry_failed=true`
```json
{}
```
