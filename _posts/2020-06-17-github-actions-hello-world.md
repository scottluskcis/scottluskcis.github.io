---
title: "GitHub Actions: Hello World"
date: 2020-06-17T20:00:00-04:00
categories:
  - github actions
tags:
  - github actions
  - devops
  - hello world
---

If you are looking for a flexible way to automate your DevOps workflows for your GitHub repositories, GitHub actions is where it's at. [GitHub Actions](https://github.com/features/actions) makes it easy to automate many of your workflows, especially in the world of DevOps. Even if you already have a DevOps story and workflows in place, it's still worth taking some time to gain a better understanding of what GitHub Actions may offer your team. As with most getting started examples, a good Hello World sample is all you need to get started. The GitHub Learning Lab course [GitHub Actions: Hello World](https://lab.github.com/githubtraining/github-actions:-hello-world) gives you just that. 

## Where to Start

As you begin to dig into GitHub Actions it's important to note two components involved in using GitHub Actions:

* the **[action](https://help.github.com/en/actions/getting-started-with-github-actions/about-github-actions#about-github-actions)** itself

> GitHub Actions help you automate your software development workflows in the same place you store code and collaborate 
> on pull requests and issues. You can write individual tasks, called actions, and combine them to create a custom 
> workflow. 

* a **[workflow](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)** that uses action(s)

> Workflows are custom automated processes that you can set up in your repository to build, test, package, release, or 
> deploy any project on GitHub. 

## supporting files

In the Hello World getting started example the action that is created will make use of a Dockerfile in your repository and in this example that Dockerfile will invoke a bash script that will print out Hello World using provided `inputs` from the action.

In this case the Docerfile looks like this:

```dockerfile
FROM debian:9.5-slim

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

And the supporting script that is invoked looks like this:

```bash
#!/bin/sh -l

sh -c "echo Hello world my name is $INPUT_MY_NAME"
```

In the example provided in the course, these supporting files live in the same directory as your `action.yml`

## action.yml

An action requires a metdata file that uses YAML syntax. This file will define the `inputs`, `outputs`, and main `entrypoint` used to run your action.

An example of what this might look like is as follows:

```yaml
name: "Hello Actions"
description: "Greet someone"
author: "octocat@github.com"

inputs:
  MY_NAME:
    description: "Who to greet"
    required: true
    default: "World"

runs:
  using: "docker"
  image: "Dockerfile"

branding:
  icon: "mic"
  color: "purple"
```

## workflow files

Workflows are defined in special files in the `.github/workflows` directory, named `main.yml`. Workflows can execute based on a chosen event. 

> Workflows piece together jobs, and jobs piece together steps. Actions can be used 
> from within the same repository, from any other public repository, or from a 
> published Docker container image. 

An example of what this might look like is as follows:

```yaml
name: A workflow for my Hello World file
on: push

jobs:
  build:
    name: Hello world action
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: ./action-a
        with:
          MY_NAME: "Mona"
```

## Summary

If we look at the order of operations here, it is as follows:

```
Workflow File -> Action File -> Dockerfile -> Bash Script
     main.yml ->  action.yml -> Dockerfile -> entrypoint.sh
```

As you completed the referenced course you'll have a working example of a GitHub Action. You will be able to see the results of this by clicking the `Actions` tab in your repository.

Be certain to check out the course [GitHub Actions: Hello World](https://lab.github.com/githubtraining/github-actions:-hello-world) for all of the details and start diving into exploring this awesome feature that GitHub offers.

I plan to start writing more posts on using GitHub Actions and wanted to be sure to put a starting point here for all who are just getting started.

## Other Notable Links
 
Other links to check out that are noted in the course are below:

* Review the [GitHub Actions documentation](https://help.github.com/articles/about-github-actions) on the GitHub Developer site.
* Use existing actions from the [GitHub Marketplace](https://github.com/marketplace/actions).
* Use existing actions from GitHub's [official actions community](https://github.com/actions).
* Use actions created by others in [awesome-actions](https://github.com/sdras/awesome-actions).