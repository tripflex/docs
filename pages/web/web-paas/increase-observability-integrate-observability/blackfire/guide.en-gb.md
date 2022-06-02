---
title: Blackfire
slug: blackfire
section: Integrate-Observability
---

**Last updated 2nd June 2022**


## Objective  

Web PaaS recommends [Blackfire.io](https://blackfire.io/) for monitoring and profiling web sites and applications. From development to test, staging and production Blackfire offers a unique blend of monitoring, often called APM, and profiling features. Blackfire supports PHP and Python.

{{< youtube bS4dzuzkir0 >}}

Across all of your sites and testing pipelines and get actionable reports and insights that help you fix issues.

Blackfire grants detailed information on the resources your application consumes across
Wall-Time, CPU, I/O, Memory, Network Calls, HTTP requests ,and SQL queries.

In addition, it can profile your code automatically and notify you
whenever your application doesn't comply with best practices for PHP, Python, and most Open-Source frameworks.

Blackfire's features are powerful especially when running on Web PaaS.
Watch the [full video tutorial](https://www.youtube.com/watch?v=Bq-LFjgD6L0).

> [!primary]  
> 
> Blackfire.io can be bundled with Enterprise and Elite subscriptions as part of the Observability Suite.
> To learn more, [contact Sales](https://platform.sh/contact/).
> All customers can also subscribe to Blackfire separately.
> 
> 

## Getting started

## Setting-up Blackfire Monitoring

Once Blackfire is configured on your cluster,
navigate to your [Organizations List](https://blackfire.io/my/organizations) > Select an Organization > Go to the Organization Monitoring Usage menu,
and activate Monitoring for the required environment.

![A screenshot of where to find Organization Monitoring Usage](images/blackfire-organization-monitoring.png "0.40")

![A screenshot of what is seen inMonitoring Activation](images/blackfire-monitoring-activation.png "0.40")

Read more in the [Blackfire documentation](https://blackfire.io/docs/monitoring-cookbooks/index) about:

* Configuring alerts
* Events API
* Monitoring third-party calls
* Monitoring consumers
* Advanced settings

## Testing the performance of each new deployment

Blackfire's native integration with Web PaaS enables you to verify the performance of your application
each time you deploy a branch, in production, staging, or development.

1\. Set up the [Blackfire Builds integration](https://blackfire.io/docs/integrations/paas/platformsh#builds-level-enterprise).

2\. Optionally, set up an [integration with your Git provider](https://blackfire.io/docs/integrations/git/index)

   and get commit status updates from build reports.
3\. [Write scenarios](https://blackfire.io/docs/builds-cookbooks/scenarios) to test business-critical use cases.


## Profiling

Blackfire lets you profile your application anywhere it is deployed,
including on your local development machines.
Using a browser extension or CLI command, profile HTTP requests, CLI scripts, Consumers, and Daemons.

Read more in the [Blackfire documentation](https://blackfire.io/docs/profiling-cookbooks/index).

## Disabling Blackfire Monitoring

Blackfire Monitoring is [enabled by default](https://blackfire.io/docs/monitoring-cookbooks/configuration#activating-monitoring-on-an-environment) for Monitoring customers.

To disable Blackfire Monitoring, create an [environment variable](../../development/variables/set-variables.md#create-environment-specific-variables).

In the management console, view the environment where you would like to disable Blackfire Monitoring
and add the variable `env:BLACKFIRE_APM_ENABLED` with the value `0`.
Otherwise, you can use the CLI command `webpaas variable:create --level environment --prefix env: --name BLACKFIRE_APM_ENABLED --value 0`

### Bypassing Reverse Proxy, Cache, and Content Delivery Networks (CDNs)

If you are using one of these tools, you need them to let Blackfire access your servers.
Read more on [how to configure a bypass](https://blackfire.io/docs/reference-guide/reverse-proxies#documentation).

### HTTP Cache configuration

If you are using the HTTP cache with cookies,
update in your `.platform/routes.yaml` the cookies that are allowed to go through the cache.
You need to allow the `__blackfire` cookie name.

Something like:

```yaml
cache:
    enabled: true
    cookies: ["/SESS.*/", "__blackfire"]
```

## Reaching out to the Blackfire support

If the above didn't help, collect the following and send it to the [Blackfire support](https://support.blackfire.io):

* The output of `webpaas ssh -- php -d display_startup_errors=on --ri blackfire` command
* The Blackfire logs

### Getting the Blackfire logs

Please execute the following in the environment where you're facing the issue:

* `webpaas variable:create php:blackfire.log_file --value /tmp/blackfire.log`
* `webpaas variable:create php:blackfire.log_level --value 4`
* start a profile/build again

You can get the logs with `webpaas ssh -- cat /tmp/blackfire.log > blackfire.log`.

### Disabling the Blackfire logs

Once you are done, disable logging by running:

* `webpaas variable:delete php:blackfire.log_file`
* `webpaas variable:delete php:blackfire.log_level`
