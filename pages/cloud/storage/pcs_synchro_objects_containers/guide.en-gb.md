---
title: Synchronise object containers
slug: pcs/sync-container
excerpt: Find here how to synchronise object containers
section: Object Storage
---

**Last updated 23rd September 2021**

## Objective

If you want to move your objects from one data centre to another, or even from one project to another, syncing objects between containers is the best solution to avoid service interruptions during your migration.

**This guide explains how you can implement this solution.**

### Requirements

- [Preparing the environment to use the OpenStack API](https://docs.ovh.com/gb/en/public-cloud/prepare_the_environment_for_using_the_openstack_api/) by downloading the Swift client
- [Setting the OpenStack environment variables](https://docs.ovh.com/gb/en/public-cloud/set-openstack-environment-variables/)
- Two object containers in two different data centres.

## Instructions

> [!warning]
>
> If your containers contain Large Objects (objects larger than 5 GB), your containers must have the same name.
>

### Configure the synchronisation

#### Creating a synchronisation key

You have to create a key and configure it on each container, so that the containers can be identified:

- Creating the key:

```bash
root@server-1:~$ sharedKey=$(openssl rand -base64 32)
```

#### Configuring the destination container

First, you have to configure the key on the container which is going to receive the data. In our case, this one is at BHS.

- Check the region which has been set in the environment variables:

```bash
root@server-1:~$ env | grep OS_REGION
OS_REGION_NAME=BHS1
```

- Configure the key on the destination container :

```bash
root@server-1:~$ swift post --sync-key "$sharedKey" containerBHS
```

You can check that it has been correctly configured using the following command:

```bash
  root@server-1:~$ swift stat containerBHS
                         Account: AUTH_b3e269xxxxxxxxxxxxxxxxxxxx2b0ba29
                       Container: containerBHS
                         Objects: 0
                           Bytes: 0
                        Read ACL:
                       Write ACL:
                         Sync To:
                        Sync Key: 4cA5j5LyaaG2ws32d1fsdQSxnvIJv+y2qFnbnm6Kw=
Meta Access-Control-Allow-Origin: https://www.ovh.com
                   Accept-Ranges: bytes
                X-Storage-Policy: Policy-0
                      Connection: close
                     X-Timestamp: 1444812373.28095
                      X-Trans-Id: B2210C05:895E_9E45A961:01BB_561E52E1_16A3:5298
                    Content-Type: text/plain; charset=utf-8
```

- Recover the address of the destination container in order to configure it on the source container:

```bash
root@server-1:~$ destContainer=$(swift --debug stat containerBHS 2>&1 | grep 'curl -i.*storage' | awk '{ print $4 }')
```

#### Configure the source container

- Change the region in the environment variables:

```bash
root@server-1:~$ export OS_REGION_NAME=GRA1
```

- Configure the key on the source container:

```bash
root@server-1:~$ swift post --sync-key "$sharedKey" containerGRA
```

- Configure the destination on the source container:

```bash
root@server-1:~$ swift post --sync-to "//OVH_PUBLIC_CLOUD/{zone}/AUTH_account/containerDest" containerGRA
```

As before, you can check that it has been correctly configured using the following command:

```bash
root@server-1:~$ swift stat containerGRA
         Account: AUTH_b3e269f057d14af594542d6312b0ba29
       Container: containerGRA
         Objects: 3
           Bytes: 15
        Read ACL:
       Write ACL:
         Sync To: //OVH_PUBLIC_CLOUD/BHS1/AUTH_b3e269f057d14af594542d6312b0ba29/containerBHS
        Sync Key: 4cA5j5LyaaG2wU4lDYnDmEwQSxnvIJv+y2qFnbnm6Kw=
   Accept-Ranges: bytes
      Connection: close
     X-Timestamp: 1444813114.55493
      X-Trans-Id: B2210C05:879E_052711B1:01BB_561E559B_24AE:6B1B
X-Storage-Policy: Policy-0
    Content-Type: text/plain; charset=utf-8
```

#### Checking the synchronisation

After a few moments (depending on the size and number of the files you are uploading) you can check if the synchronisation worked by listing the files in each container.

- List the files in the source container :

```bash
root@server-1:~$ swift list containerGRA
test1.txt
test2.txt
test3.txt
```

- List the files in the destination container :

```bash
root@server-1:~$ swift list containerBHS
test1.txt
test2.txt
test3.txt
```

> [!success]
>
> You can also use this guide to migrate objects from RunAbove to Public Cloud.
>

## Go further

Join our community of users on <https://community.ovh.com/en/>.
