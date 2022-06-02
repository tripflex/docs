---
title: Add services
slug: add-services
section: Add-Services
hidden: true
---

**Last updated 2nd June 2022**



## Objective  

Web PaaS includes many services, so you don't have to subscribe to external cache or search engine services.
Because the services are included in your project, you can manage them through Git
and they're backed up together with the rest of your project.

Your project defines the services configuration in a file named `services.yaml` located in the `.platform` directory.
If you don't need any services (such as for a static website), you don't need to include this file in your repository.

Read on to see how to add services.

![Services](images/relationships.png "0.50")

## Add a service

Adding a service is a two-step process.

### 1. Configure the service

All service configuration happens in the services configuration file (`.platform/services.yaml`) in your Git repository.

Configure your service in the following pattern:

```yaml {location=".platform/services.yaml"}
<SERVICE_NAME>:
    type: <SERVICE_TYPE>:<VERSION>
    # Other options...
```

An example service configuration for two databases might look like this:

```yaml {location=".platform/services.yaml"}
database1:
    type: mariadb:10.5
    disk: 2048
database2:
    type: postgresql:13
    disk: 1024
```

This YAML file is a dictionary defining all of the services you want to use.
The top-level key is a custom service name, which you use to identify the service in step 2.
You can give it any name you want with lowercase alphanumeric characters, hyphens, and underscores.

> [!primary]  
> 
> Changing the service name is interpreted as creating an entirely new service.
> This **removes all data in that service**.
> Always back up your data before changing existing services in your `.platform/services.yaml` file.
> 
> 

#### Service options

The following table presents the keys you can define for each service:

| Name            | Type       | Required          | Description |
| --------------- | ---------- | ----------------- | ----------- |
| `type`          | `string`   | Yes               | One of the [available services](#available-services) in the format `type:version`. |
| `disk`          | `integer`  | For some services | The size in MB of the [persistent disk](#disk) allocated to the service. Can't be set for memory-resident-only services such as `memcache` and `redis`. Limited by your plan settings. |
| `size`          | `string`   |                   | How many CPU and memory [resources to allocate](#size) to the service. Possible values are `AUTO` (default), `S`, `M`, `L`, `XL`, `2XL`, and `4XL`. Limited by your plan settings. |
| `configuration` | dictionary | For some services | Some services have additional specific configuration options that can be defined here, such as specific endpoints. See the given service page for more details. |
| `relationships` | dictionary | For some services | Some services require a relationship to your app. The content of the dictionary has the same type as the `relationships` dictionary for [app configuration](../create-apps/app-reference.md#relationships). The `endpoint_name` for apps is always `http`. |

##### Disk

Downsizing a service's persistent disk isn't currently supported
in the `eu.platform.sh` and `us.platform.sh` regions.
If necessary, [migrate your project](../projects/region-migration.md).

##### Size

By default, Web PaaS allocates CPU and memory resources to each container automatically
given the available resources in [your plan](../overview/pricing/_index.md).
Some services are optimized for high CPU load, some for high memory load.
If your plan is sufficiently large for bigger containers, you can increase the size of your service container.

Note that service containers in development environments are always set to size `S`.

### 2. Connect the service

Once you have configured a service, you need to create a relationship to connect it to an app.
This is done in your [app configuration for relationships](../create-apps/app-reference.md#relationships).

The relationship follows this pattern:

```yaml {location=".platform.app.yaml"}
relationships:
    <RELATIONSHIP_NAME>: "<SERVICE_NAME>:<ENDPOINT>"
```

An example relationship to connect to the databases given in the [example in step 1](#1-configure-the-service):

```yaml {location=".platform.app.yaml"}
relationships:
    mysql_database: "database1:mysql"
    postgresql_database: "database2:postgresql"
```

As with the service name, you can give the relationship any name you want
with lowercase alphanumeric characters, hyphens, and underscores.
It helps if the service name and relationship name are different, but it isn't required.

Each service offers one or more endpoints for connections, depending on the service.
An endpoint is a named set of credentials to give access to other apps and services in your project.
If you don't specify one in the [service configuration](#service-options), a default endpoint is created.
The default endpoint varies by service, generally being its type (such as `mysql` or `solr`).

## Available services

The following table presents the available service types and their versions.
Add them to the `type` key of the [service configuration](#1-configure-the-service) in the format `type:version`.

<!-- To update the versions in this table, use docs/data/registry.json -->
| **Service** | **`type`** | **Supported `version`** |
|----------------------------------|---------------|-------------------------|
| [Headless Chrome](add-services-headless-chrome) | `chrome-headless` | 73, 80, 81, 83, 84, 86, 91 |
| [Elasticsearch](add-services-elasticsearch) | `elasticsearch` |  |
| [InfluxDB](add-services-influxdb) | `influxdb` | 1.2, 1.3, 1.7, 1.8 |
| [Kafka](add-services-kafka) | `kafka` | 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7 |
| [MariaDB/MySQL](add-services-mysql) | `mariadb` | 10.0, 10.1, 10.2, 10.3, 10.4, 10.5 |
| [Memcached](add-services-memcached) | `memcached` | 1.4, 1.5, 1.6 |
| [MongoDB](add-services-mongodb) | `mongodb` |  |
| [MongoDB](add-services-mongodb) | `mongodb-enterprise` | 5.0 |
| [Network Storage](add-services-network-storage) | `network-storage` | 2.0 |
| [OpenSearch](add-services-opensearch) | `opensearch` | 1.1, 1.2 |
| [Oracle MySQL](add-services-mysql) | `oracle-mysql` | 5.7, 8.0 |
| [PostgreSQL](add-services-postgresql) | `postgresql` | 9.6, 10, 11, 12, 13 |
| [RabbitMQ](add-services-rabbitmq) | `rabbitmq` | 3.5, 3.6, 3.7, 3.8, 3.9 |
| [Redis](add-services-redis) | `redis` | 3.2, 4.0, 5.0, 6.0, 6.2, 7.0 |
| [Solr](add-services-solr) | `solr` | 7.7, 8.0, 8.4, 8.6 |
| [Varnish](add-services-varnish) | `varnish` | 5.1, 5.2, 6.0, 6.3 |
| [Vault KMS](add-services-vault) | `vault-kms` | 1.6, 1.8 |

## Service timezones

All services have their system timezone set to UTC by default.
For some services, you can change the timezone for the running service
(this doesn't affect the container itself and so logs are still in UTC).

* [MySQL](./mysql/_index.md#service-timezone)
* [PostgreSQL](./postgresql.md#service-timezone)

## Connect to a service

Once a service is running and exposed as a relationship,
its appropriate credentials (such as the host, username, and password) are available through the `PLATFORM_RELATIONSHIPS` environment variable.
The structure of each is documented on the appropriate service's page along with sample code for how to connect to it from your application.

The keys in the `PLATFORM_RELATIONSHIPS` variable are fixed, but the values may change on deployment or restart.
So you should check the environment variable every time your script or app starts.

Access to the database or other services is only available from your apps.
For security reasons, they can't be accessed directly.

Connecting to a service is a two-step process.

### 1. Obtain service credentials

To get the credentials for a given service, run the following command
(replacing `<PROJECT_ID>` and `<ENVIRONMENT_NAME>` with appropriate values):

```bash
$ webpaas relationships -p <PROJECT_ID> -e <ENVIRONMENT_NAME>
database:
    -
        username: user
        scheme: mysql
        service: database
        fragment: null
        ip: 246.0.80.37
        hostname: e3wffyxtwnrxujeyg5u3kvqi6y.database.service._.us.platformsh.site
        public: false
        cluster: jyu7waly36ncj-main-7rqtwti
        host: database.internal
        rel: mysql
        query:
            is_master: true
        path: main
        password: ''
        type: 'mariadb:10.5'
        port: 3306
        host_mapped: false
        url: 'mysql://user:@database.internal:3306/main'
```

With this example, you can connect to the `database` relationship
with the user `user`, an empty password, and the database name `main` (from the `path`).
Find a full database connection in the `url` property.

### 2. Connect to an SSH tunnel

Once you have [opened an SSH tunnel](../development/ssh/_index.md#connect-to-services),
you can use it to connect to your service.

If you have a direct tunnel,
you might connect to `127.0.0.1:30000` to the `main` database with the username `user` and an empty password.

If you have an app tunnel, you might SSH to `ssh.us.platform.sh` as user `jyu7waly36ncj-main-7rqtwti--app`
and then connect to host `database.internal` to the `main` database with the username `user` and an empty password.
