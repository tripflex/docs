---
title: Memcached (Object cache)
slug: add-services-memcached
section: Add-Services
---

**Last updated 2nd June 2022**


## Objective  

Memcached is a simple in-memory object store well-suited for application level caching.


See the [Memcached documentation](https://memcached.org) for more information.

Both Memcached and Redis can be used for application caching. As a general rule, Memcached is simpler and thus more widely supported while Redis is more robust. Web PaaS recommends using Redis if possible but Memcached is fully supported if an application favors that cache service."

{{% frameworks %}}

- [Drupal](../guides/drupal9/memcached.md)


{{% /frameworks %}}

## Supported versions

| **Grid** |  **Dedicated Generation 3** |
|----------------------------------|  
|  1.4 |  
|  1.5 |  
|  1.6 |  

{{< image-versions-legacy "memcached" >}}

## Relationship

The format exposed in the ``$PLATFORM_RELATIONSHIPS`` [environment variable](../development/variables/use-variables.md#use-platformsh-provided-variables):

```json  
{
    "service": "memcached16",
    "ip": "169.254.66.74",
    "hostname": "3sdm72jgaxge2b6aunxdlzxyea.memcached16.service._.eu-3.platformsh.site",
    "cluster": "rjify4yjcwxaa-master-7rqtwti",
    "host": "memcached.internal",
    "rel": "memcached",
    "scheme": "memcached",
    "type": "memcached:1.6",
    "port": 11211
}
```  

## Usage example

{{% endpoint-description type="memcached" php=true python=true /%}}

> [!tabs]      
> Go     
>> ``` go     
>> {!> web/web-paas/static/files/fetch/examples/golang/memcached !}  
>> ```     
> Java     
>> ``` java     
>> {!> web/web-paas/static/files/fetch/examples/java/memcached !}  
>> ```     
> PHP     
>> ``` php     
>> {!> web/web-paas/static/files/fetch/examples/php/memcached !}  
>> ```     
> Python     
>> ``` python     
>> {!> web/web-paas/static/files/fetch/examples/python/memcached !}  
>> ```     


