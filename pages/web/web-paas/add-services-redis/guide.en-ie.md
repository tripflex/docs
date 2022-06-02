---
title: Redis (Object cache)
slug: add-services-redis
section: Add-Services
---

**Last updated 2nd June 2022**



## Objective  

Redis is a high-performance in-memory object store, well-suited for application level caching.

See the [Redis documentation](https://redis.io/documentation) for more information.

Web PaaS supports two different Redis configurations:

- Persistent: useful for key-value application data

- Ephemeral: in-memory only, useful for application caching


Otherwise, they're identical.

{{% frameworks %}}

- [Drupal](../guides/drupal9/redis.md)

- [Ibexa DXP](../guides/ibexa/deploy.md#cache-and-sessions)

- [Jakarta EE](../guides/jakarta/deploy.md#redis)

- [Micronaut](../guides/micronaut/redis.md)

- [Quarkus](../guides/quarkus/redis.md)

- [Spring](../guides/spring/redis.md)

- [WordPress](../guides/wordpress/redis.md)


{{% /frameworks %}}

## Supported versions

| **Grid** |  **Dedicated Generation 3** |
|----------------------------------|  
|  3.2 |  
|  4.0 |  
|  5.0 |  
|  6.0 |  
|  6.2 |  
|  7.0 |  

{{< image-versions-legacy "redis" >}}

{{% deprecated-versions %}}

| **Grid** |  **Dedicated Generation 3** |
|----------------------------------|  
|  2.8 |  
|  3.0 |  

> [!primary]  
> Versions 3.0 and higher support up to 64 different databases per instance of the service, but Redis 2.8 is configured to support only a single database.
> 

## Service types

There are two types of the Redis service depending on what you want to do with it.

### Ephemeral Redis

The ephemeral Redis service is configured to serve as a cache; its storage isn't persistent.

Data in an ephemeral Redis instance is stored only in memory and thus requires no disk space.
When the service hits its memory limit,
it automatically evicts cache items according to the [configured eviction rule](#eviction-policy) to make room for new ones.

Your app must not treat ephemeral Redis as permanent.
Instead, the cache needs to be regenerated as necessary.
For example, if a container is moved for a reason such as region maintenance,
the `deploy` and `post_deploy` hooks don't run and an app that treats the cache as permanent shows errors.
The cache should be cleared each time the app is restarted,
in the `start` key in [your web configuration](../create-apps/app-reference.md#web-commands).

If your app needs to treat the cache as permanent, use [persistent Redis](#persistent-redis),
which saves data to its volume even when the container is shut down.

### Persistent Redis

The `redis-persistent` service type is configured for persistent storage.
That makes it a good choice for fast application-level key-value storage.

> [!primary]  
> 
> You can't switch a service from persistent to ephemeral.
> To switch between modes, use a different service with a different name.
> 
> 

## Relationship

The format exposed in the ``$PLATFORM_RELATIONSHIPS`` [environment variable](../development/variables/use-variables.md#use-platformsh-provided-variables):

```json  
{
    "username": null,
    "scheme": "redis",
    "service": "redis6",
    "fragment": null,
    "ip": "169.254.175.235",
    "hostname": "7mnenhdiz7ecraovljrba6pmiy.redis6.service._.eu-3.platformsh.site",
    "public": false,
    "cluster": "rjify4yjcwxaa-master-7rqtwti",
    "host": "redis.internal",
    "rel": "redis",
    "query": [],
    "path": null,
    "password": null,
    "type": "redis:6.0",
    "port": 6379,
    "host_mapped": false
}
```  

The format is identical regardless of whether it's a persistent or ephemeral service.

## Usage example

{{% endpoint-description type="redis" php=true /%}}

> [!tabs]      
> Java     
>> ``` java     
>> {!> web/web-paas/static/files/fetch/examples/java/redis !}  
>> ```     
> PHP     
>> ``` php     
>> {!> web/web-paas/static/files/fetch/examples/php/redis !}  
>> ```     
> Python     
>> ``` python     
>> {!> web/web-paas/static/files/fetch/examples/python/redis !}  
>> ```     

## Multiple databases

Redis 3.0 and above are configured to support up to 64 databases.
Redis doesn't support distinct users for different databases
so one relationship connection gives access to all databases.

The way to access a particular database depends on the [client library](https://redis.io/clients) you're using:

> [!tabs]      
> PHP     
>> ``` false     
>> 
>> 
>> Use the Redis [`select` command](https://redis.io/commands/select):
>> 
>> ```php
>> <?php
>> $redis->select(0);       // switch to DB 0
>> $redis->set('x', '42'); // write 42 to x
>> $redis->move('x', 1);  // move to DB 1
>> $redis->select(1);    // switch to DB 1
>> $redis->get('x');    // will return 42
>> ```
>> 
>> 
>> ```     
> Python     
>> ``` false     
>> 
>> 
>> To manage [thread safety](https://github.com/redis/redis-py#user-content-thread-safety),
>> the Python library suggests using separate client instances for each database:
>> 
>> ```python
>> from redis import Redis
>> from platformshconfig import Config
>> 
>> # Get the credentials to connect to the Redis service.
>> config = Config()
>> credentials = config.credentials('redis')
>> 
>> database0 = Redis(host='xxxxxx.cache.amazonaws.com', port=6379, db=0)
>> database1 = Redis(host='xxxxxx.cache.amazonaws.com', port=6379, db=0)
>> ```
>> 
>> 
>> ```     

## Eviction policy

On the Ephemeral `redis` service it's also possible to select the key eviction policy.
That controls how Redis behaves when it runs out of memory for cached items and needs to clear old items to make room.

```yaml
cache:
    type: redis:5.0
    configuration:
        maxmemory_policy: allkeys-lru
```

The default value if not specified is `allkeys-lru`, which removes the oldest cache item first.
The following values are allowed:

* `noeviction`
* `allkeys-lru`
* `volatile-lru`
* `allkeys-lfu` _(Available as of [Redis 4.0](https://redis.io/topics/lru-cache#the-new-lfu-mode))_
* `volatile-lfu` _(Available as of [Redis 4.0](https://redis.io/topics/lru-cache#the-new-lfu-mode))_
* `allkeys-random`
* `volatile-random`
* `volatile-ttl`

See the [Redis documentation](https://redis.io/topics/lru-cache#eviction-policies) for a description of each option.

### Using Redis as handler for native PHP sessions

Using the same configuration but with your Redis relationship named `sessionstorage`:


```yaml   
data:
    type: redis-persistent:7.0
    disk: 256
```  


`.platform.app.yaml`

```yaml
relationships:
    sessionstorage: "data:redis"

variables:
    php:
        session.save_handler: redis
        session.save_path: "tcp://sessionstorage.internal:6379"
```
