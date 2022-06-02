---
title: Rename the default environment
slug: environments-default-environment
section: Environments
---

**Last updated 2nd June 2022**



## Objective  

You can set the name of your default/production branch when creating a project.
To change it after project creation, follow the steps below.

You can complete some of these steps through the management console,
but since all can be completed with the CLI those commands alone are listed.
Be sure to [install the CLI](../development/cli/_index.md) if you haven't already done so.
It's assumed you are changing the default branch of your project on Web PaaS from `master` to `main`.
If using another name for the default branch, update the commands accordingly.

> [!primary]  
> 
> If you have a domain set for your default environment, remove it before changing the default branch.
> Otherwise you get an error that `default domain must be a valid project domain`.
> 
> 

## Projects without external integrations

The steps below assume:

- You already have a project on Web PaaS containing your code.

- No external [source integrations](../integrations-source) have been set up for the project.

  That is, the Web PaaS project contains your primary remote repository for the site. 

### 1. Create the Main environment

To start, create a new environment off `master` called `main`:

```bash
$ webpaas environment:branch main master --title=Main -p <PROJECT_ID> --force
```

> [!primary]  
> 
> The CLI assumes that you are running this command within a local copy of your repository,
> so the `--force` flag is included above to bypass that.
> 
> 

`main` is currently the child of `master`, so use the below command to remove its parent and make it a top-level branch:

```bash
$ webpaas environment:info -p <PROJECT_ID> -e main parent -
```

At this point, all of your environments, both active and inactive, are still children of `master`.
In the next step, you deactivate `master`, which doesn't work if it still has child environments associated with it.
Before going any further, take the time to update each environment's parent to `main`:

```bash
$ webpaas environment:info -p <PROJECT_ID> -e <BRANCH> parent main
```

![Update the parent environment](images/main-newparent.png "0.75")

### 2. Reconfigure the default branch

First, you need to deactivate the `master` environment with the following CLI command:

```bash
$ webpaas environment:delete master --no-delete-branch -p <PROJECT_ID>
```

Once `master` has been deactivated, you can then set the project's `default_branch` property to `main`:

```bash
$ webpaas project:info default_branch main -p <PROJECT_ID>
```

Finally, delete the `master` environment completely. 

```bash
$ webpaas environment:delete master --delete-branch -p <PROJECT_ID>
```

## Setting up an external integration on a new project

The steps below assume:

- You have an external repository on GitHub, GitLab, or BitBucket you are trying to integrate with Web PaaS.

- That repository's default branch isn't `master`.


### 1. Create a new project

First, create an empty project with the Web PaaS CLI command `webpaas create`:

```bash
$ webpaas create --title='Main Project' --region=us-3.platform.sh --plan=development --environments=3 --storage=5 --no-set-remote
```

This creates a new project with `master` as the default branch by default.
Modify the flags to fit your use case, or skip them and the CLI will ask you to set them individually during creation.
Copy the `Project ID` provided when the command completes, and substitute the value in the steps below.
You can also visit the provided management console URL for the project (`https://console.platform.sh/<USER>/<PROJECT_ID>`)
to verify the steps as you go along.

The project you just created is empty, with no code initialized in its `master` environment.
You need to have something on that environment to begin with in order to create the `main` branch,
so you can initialize it with a template for now:

```bash
$ webpaas environment:init -p <PROJECT_ID> -e master https://github.com/platformsh-templates/hugo.git
```

Then, create the `main` environment:

```bash
$ webpaas environment:branch main master --title=Main -p <PROJECT_ID> --force
```

> [!primary]  
> 
> The CLI assumes that you are running this command within a local copy of your repository,
> so the `--force` flag is included above to bypass that.
> 
> 

`main` is currently a child of `master`, so use the below command to remove its parent and make it a top-level branch:

```bash
$ webpaas environment:info -p <PROJECT_ID> -e main parent -
```

### 2. Deactivate the `master` environment

You need to deactivate the `master` environment with the following CLI command:

```bash
$ webpaas environment:delete master --no-delete-branch -p <PROJECT_ID>
```

> [!primary]  
> 
> Deactivating any environment is a [destructive operation](../administration-web/configure-environment#status)
> and can result in data loss.
> Since you are deactivating what was once the production branch for your site,
> it's recommended that you perform the switch to a new default branch during non-peak hours for your site.
> As always, be sure to take a [backup](../administration-backup-and-restore#backups) of `master` beforehand;
> you can always [restore this backup](../administration-backup-and-restore#restore) to `main` afterwards. 
> 
> 

### 3. Make `main` the default branch

First, update the project's `default_branch` property to `main`:

```bash
$ webpaas project:info default_branch main -p <PROJECT_ID>
```

At this point, while the repository considers `main` to be the default branch for the project,
`master` is still considered to be the parent branch of `main`.
You can change this by updating `main` to have a parent of `null` with the command:

```bash
$ webpaas environment:info -p <PROJECT_ID> -e main parent -
```

You have already deactivated Master,
so all that's left now is to delete the `master` branch from the remote repository with one final authenticated request.

```bash
$ webpaas environment:delete master --delete-branch -p <PROJECT_ID>
```

You are then left with `main` as your top-level parent environment for the project.

![Default environment main](images/defaultbranch-main.png "0.75")

### 4. Setup the integration 

Place the command to integrate Web PaaS with your external [GitHub](../integrations-source/github),
[GitLab](../integrations-source/gitlab), or [BitBucket](../integrations-source/bitbucket) repository
that has already been set up with `main` as the default branch.
Select the settings you would like for the integration, but in most cases the default settings are best.
The last stage gives a warning: 

```bash
Warning: adding a 'github' integration will automatically synchronize code from the external Git repository.
This means it can overwrite all the code in your project.

Are you sure you want to continue? [y/N] y
```

This overrides the code on the `main` environment (the template you started with)
with the contents of the `main` branch of your external repository.
You can now consult the rest of the [migration steps](../tutorials-migrating) to finish migrating your site to Web PaaS.

## Updating externally integrated projects

The steps below assume:

- You have already integrated an external repository GitHub, GitLab, or BitBucket.

- You are trying to safely change the name of your default branch from `master` to `main`

  on both your external repository and on a Web PaaS project.

### 1. Create the `main` branch

Within your local copy of the external repository, create the main branch and push:

```bash
$ git checkout master && git pull origin master
$ git checkout -b main
$ git push origin main
```

Then activate it on Web PaaS

```bash
$ webpaas environment:activate main -p <PROJECT_ID>
```

### 2. Clean up repository

Most external services don't yet automatically remap to a new default branch.
To preserve your data on Web PaaS,
it's best to take a moment to prepare all of your work in progress around `master` to instead compare to `main`. 

First, close any open pull requests and resubmit them against `main`.
Be sure to rebase your local branches with `git rebase --onto main master`
if you still plan on continuing to work on them after the default branch switch.
Once you resubmit them, they appear under the `main` environment on Web PaaS. 

![Resubmitted PRs](images/resubmit-prs.png "0.75")

### 3. Change the default branch on the external Git service

Web PaaS supports external Git integrations to a number of services,
so follow the linked instructions below for changing the default branch to `main` for your provider:

- [GitHub](https://github.com/github/renaming)

- [GitLab](https://docs.gitlab.com/ee/user/project/repository/branches/default.html#change-the-default-branch-name-for-a-project)

- [BitBucket](https://community.atlassian.com/t5/Bitbucket-questions/How-to-change-MAIN-branch-in-BitBucket/qaq-p/977418)


### 4. Deactivate the `master` environment

You need to deactivate the `master` environment.
Since it 's currently the default branch, it's protected,
so the normal `webpaas environment:delete` CLI command doesn't at this stage.
This restriction will soon be removed,
but for now you can get around this by placing an authenticated cURL request on the project's API
with the following CLI command to deactivate it:

```bash
$ webpaas project:curl -X POST -p <PROJECT_ID> environments/master/deactivate 
```

> [!primary]  
> 
> Deactivating any environment is a [destructive operation](../administration-web/configure-environment#status) and can result in data loss.
> Since you are deactivating what was once the production branch for your site,
> it's recommended that you perform the switch to a new default branch during non-peak hours for your site.
> As always, be sure to take a [backup](../administration-backup-and-restore#backups) of `master` beforehand;
> you can always [restore this backup](../administration-backup-and-restore#restore) to `main` afterwards. 
> 
> 

### 5. Make `main` the default branch

Update the project's `default_branch` property with another authenticated request:

```bash
$ webpaas project:info default_branch main -p <PROJECT_ID>
```

At this point, while the repository considers `main` to be the default branch for the project,
`master` is still considered to be the parent branch of `main`.
You can change this by updating `main` to have a parent of `null` with the command:

```bash
$ webpaas environment:info -p <PROJECT_ID> -e main parent -
```

![Make main a parent](images/existing-parentnull.png "0.75")

You can now delete the `master` branch from your external repository.
