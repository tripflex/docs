---
title: Set up your local development environment
slug: development-local
section: Development
order: 5
---

**Last updated 2nd June 2022**


## Objective  

While Web PaaS is great as a tool for hosting an application during both development and production, it's naturally not the ideal place to edit code.  You can't, in fact, as the file system is read-only (as it should be). The proper place to edit your code is on your computer.


You need to have both [Git](../development-tools#git) and the [Web PaaS CLI](../development-cli) installed before continuing.

## Download the code

If you don't already have a local copy of your project's code, run `webpaas get` to download one. You can also run `webpaas projects` to list all of the projects in your account.

```bash
~/htdocs $ webpaas projects
Your projects are:
+---------------+----------------------------+------------------------------------------------+
| ID            | Name                       | URL                                            |
+---------------+----------------------------+------------------------------------------------+
| [project-id]  | New WebPaas Project       | https://eu.webpaas.ovhcloud.com/#/projects/[project-id] |
+---------------+----------------------------+------------------------------------------------+

Get a project by running webpaas get [id].
List a project's environments by running webpaas environments.
```

Now you can download the code using `webpaas get [project-id] [folder-name]`:

```bash
~/htdocs $ webpaas get [project-id] my-project
Cloning into 'my-project/repository'...
remote: counting objects: 11, done.
Receiving objects: 100% (11/11), 1.36 KiB | 0 bytes/s, done.
Checking connectivity... done.
```

You should now have a repository folder, based on what you used for *[folder-name]* in the `webpaas get` command above.

You will also notice a new directory in your project, `.platform/local`, which is excluded from Git.  This directory contains builds and any local metadata about your project needed by the CLI.

## Building the site locally

Run the `webpaas build` command to run through the same build process as would be run on Web PaaS.
This creates a `_www` directory in your project root
that's a symbolic link to the currently active build in the `.platform/local/builds` folder.
Run your local server from the `www` directory.

```bash
$ webpaas build

Building application app (runtime type: golang:1.17)
Running post-build hooks
Creating symbolic links to mimic shared file mounts
  Symlinking .cache to .platform/local/shared/cache

Build complete for application app
Web root: <PATH_TO_PROJECT>/_www
```

Because the `webpaas build` command runs locally,
you need to have any runtime or tools used in your build process available in your local environment
or the build fails.

The process may also result in side effects,
such as the installation on your local computer of packages referenced in your `dependencies` block.
If you don't want that, use a local virtual machine
as an enclosed local development environment that doesn't affect your main system.

## Running the code

Web PaaS supports whatever local development environment you wish to use.  There is no dependency on any particular tool so if you already have a local development workflow you're comfortable with you can keep using it without changes.  That's the "[untethered](untethered)" option.

For quick changes, you can also run your code locally but use the services hosted on Web PaaS.  That is, your site is "[tethered](tethered)" to Web PaaS.  While this approach requires installing less on your system it can be quite slow as all communication with the database or cache server will need to travel from your computer to Web PaaS's servers.


