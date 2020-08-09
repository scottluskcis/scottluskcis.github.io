---
title: "Running the Cosmos Emulator in Docker Containers"
date: 2020-08-09T16:00:00-04:00
categories:
  - cosmos
  - docker
  - environment setup
tags:
  - Cosmos
  - Cosmos Emulator
  - Docker
  - Hyper-V
---

If you've worked with [Azure Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/) at all in a development setting then you have certainly come across the [Azure Cosmos Emulator](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator) for **local development and testing**. Most developers I speak with follow the typicall installation and setup for running the emulator and possibly that is your situation as well, but what else is possible? Did you know that it's possible to run the Cosmos Emulator inside of a [Docker](https://www.docker.com/) Container?

If you already have Docker Desktop for Windows installed and are not running this in a Hyper-V VM as mentioned in the [background](#background) you can skip ahead to the section on [Setup the Cosmos Emulator in a Docker Container](#setup-the-cosmos-emulator-in-a-docker-container)

## Background

My typical development environment setup always involves standing up a [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/) Virtual Machine dedicated to a specific client or project that I am working on. This allows me to ensure that I am keeping a client project and all that is related to it isolated from other projects, per a best practice followed by the awesome team that I am a part of. Of course, with the use of Containers these days perhaps Hyper-V will be put back in the toolbox at some point in the future, but for now its a vital tool we rely on.

Up to this point, only needing the Cosmos Emulator to [run on Windows](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#running-on-windows) has kept my installation and setup of the emulator pretty basic. In a recent project however, I encountered the need to develop against both the [SQL API](https://docs.microsoft.com/en-us/azure/cosmos-db/create-sql-api-dotnet) and the [Table API](https://docs.microsoft.com/en-us/azure/cosmos-db/table-introduction) at the same time. What does this look like in a local development environment using the Cosmos Emulator?

The Cosmos Emulator gives you the ability to start the emulator running under each of the supported APIs. For example, if you wanted to start the emulator locally using the [Table API](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#table-api) you would start the emulator from a command prompt as an administrator with `/EnableTableEndpoint` passed with the arguments. By default the emulator will start   with the SQL API enabled. Other possibilities include: `/EnableMongoDbEndpoint`, `/EnableTableEndpoint`, `/EnableCassandraEndpoint`, or `/EnableGremlinEndpoint` and can be specified to support the API you are developing against. You can only run the emulator under one API at a time though.

While this is helpful it didnt't solve the problem at hand. In my scenario, I needed to run both the SQL API and the Table API *at the same time*. Doing some research and digging into the possibilities it was obvious that [Running on Docker](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#running-on-docker) using [Docker for Windows](https://www.docker.com/products/docker-desktop) might provide me the ability to accomplish what was needed. The challenge however was how to make this happen on a Hyper-V VM in our current setup.

## Nested Virtualization

It's possible to [Enable Nested Virtualization](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) to allow you to run Hyper-V inside of a Hyper-V virtual machine (VM). If you are [Installing Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/) the System Requirements include that `Hyper-V and Containers Windows features must be enabled`. In this scenario **Nested Virtualization** is necessary in order to support installation of Docker Desktop on Windows inside of a Hyper-V VM. 

Details are documented at the following links on things to consider in this scenario but the rest of this article will walk through the steps taken to enable this in the local development environment referenced in the [background](#background) section.

* [Running Docker Desktop in nested virtualization scenarios](https://docs.docker.com/docker-for-windows/troubleshoot/#running-docker-desktop-in-nested-virtualization-scenarios)
* [Run Hyper-V in a Virtual Machine with Nested Virtualization](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

> **IMPORTANT**: The approach outlined in this article is ONLY recommended for a local development environment. It is NOT recommended that you try this in a non-development environment. Read the referenced articles in greater detail and understand what nested virtualization is but again, use this only for your local development and troubleshooting needs! You may still find the use of Docker Containers for running the Cosmos Emulator could be beneficial (e.g. DevOps Pipelines) but for enabling nested virtualization to accomplish this is the part to take note of.

## Configuring Local Development Environment

For this section, I will walk through the steps taken to setup running the Cosmos Emulator in a Docker Container inside of a Hyper-V VM.

### Enable Nested Virtualization

If you are not going to install Docker Desktop for Windows on a Hyper-V VM then you can skip this section.

In this scenario we are going to install Docker Desktop for Windows on a Hyper-V VM. For this to work we must first **Enable Nested Virtualization**. This article: [How to Run Docker on a Windows 10 Pro Hyper-V Virtual Machine](https://blog.michaeldeongreen.com/static-pages/how-to-run-docker-on-a-windows-10-pro-hyper-v-virtual-machine.html) does a good job of discussing this topic and I'm going to walk thru some of the same steps below.

As an alternative to the steps below you can also work thru the documentation at [Run Hyper-V in a Virtual Machine with Nested Virtualization](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

STEPS: 

1. Download the script to enable Nested Virtualization from [here](https://github.com/MicrosoftDocs/Virtualization-Documentation/blob/live/hyperv-tools/Nested/Enable-NestedVm.ps1) and save it to your **Host** machine
2. On your **Host** machine, open up Hyper-V
3. Enusre that your Windows VM that you will be configuring is _not_ running (NOTE: the steps outlined in this article are only applicable to a Windows VM)
4. Make note of the **Name** of the Windows VM that you are going to install Docker for Windows on
5. Open up a **Powershell** command prompt *as an administrator*
6. Navigate (`cd`) to the location where you saved the script mentioned in step 1
7. Execute the script replacing *vm-name* with the Name noted in step 4
    ```powershell
    .\Enable-NestedVm.ps1 {vm-name}
    ```
8. Confirm that the script completed successfully
9. In Hyper-V **Connect** to your VM
10. Proceed to [Install Docker Desktop for Windows](#install-docker-desktop-for-windows)

> **NOTE**: If you get an error about Execution Policy, you may need to check the current policy set for your machine. Refer to [About Execution Policies](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7) for details. Always review any scripts before executing them. If you do need to change an execution policy I recommending to only [Set a different policy for one session](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7#set-a-different-policy-for-one-session) so that only the current session is affected.

### Install Docker Desktop for Windows

Next, we move on to installing Docker Desktop. If installing this on a Hyper-V VM, be sure you have completed the section on [Enable Nested Virtualization](#enable-nested-virtualization) first.

STEPS: 

1. Connect to your machine where Docker Desktop is to be installed
2. Install Docker Desktop, there are a few ways to do this, below are a few options:
   1. Follow the steps at [Install Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/) 
    or
   2. Use Chocolatey to install [Docker Desktop](https://chocolatey.org/packages/docker-desktop/)
3. Restart your machine and wait for all updates to finish installing
4. After Docker for Windows has been installed and your machine has restartd, you should see that Docker is starting in your system tray, wait for this to finish and  see an indicator that Docker is Running.
5. Follow any initial prompts (e.g. sign in with your Docker account, Getting Started tutorial, etc.)
6. Right click on the Docker icon in your system tray and choose **Switch to Windows containers...**
7. Open PowerShell and enter the following:
    ```powershell
    docker pull hello-world && docker run hello-world
    ```
8. Confirm that the output indicates that your installation appers to be working correctly.
9. Move on to the next steps

### Setup the Cosmos Emulator in a Docker Container

With Docker Desktop for Windows installed you can setup the Cosmos Emulator Docker Container. These steps are documented in greater detail at [Running on Docker](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#running-on-docker).

STEPS:

1. Go to the machine where Docker Desktop is installed
2. Make sure that Docker is running
3. Make sure that Docker is set to run Windows Containers
4. Open Powershell
5. Run the following command and wait for the image to download
    ```powershell
    docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator 
    ```
6. To start the image, run the following commands:
    ```powershell
    md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null
    docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
    ```
7. Note the response generated from starting the image
8. Next you will need to import the TLS/SSL certificate using the following command:
    ```powershell
    cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
    .\importcert.ps1
    ```
9. Open a browser and navigate to `https://<emulator endpoint provided in response>/_explorer/index.html`

> **NOTE:** Closing the interactive shell once the emulator has been started will shut down the emulator's container.

### Next Steps, Running multiple Emulators

Now that we know we can [Setup the Cosmos Emulator in a Docker Container](#setup-the-cosmos-emulator-in-a-docker-container) lets revisit the original goal here. We want to be able to run an instance of emulator under SQL API and another instance of the emulator using Table API. We can accomplish this using the `/EnableTableEndpoint` argument and also make sure we run the second instance of the emulator under a different port using `-p`, for example `-p 443:8082` or whatever port you want to use.

## Final Thoughts

Using Containers gives us some flexibility here to solve the problem that we were facing. As mentioned previously, the approach outlined in this post is recommended only for local development and troubleshooting purposes. There are many other reasons you may want to run the Cosmos Emulator in a Docker Container as well though. How are you running the Cosmos Emulator in your local environment? Are you using the Docker Container image for Cosmos Emulator for other things such as DevOps?

If you gain nothing else from this post be sure to note that this approach is a possibility and bookmark the documentation: 
* [Use the Azure Cosmos Emulator for local development and testing - Running on Docker](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#running-on-docker).

## References

* [Use the Azure Cosmos Emulator for local development and testing - Running on Docker](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator#running-on-docker)
* [Running Docker Desktop in nested virtualization scenarios](https://docs.docker.com/docker-for-windows/troubleshoot/#running-docker-desktop-in-nested-virtualization-scenarios)
* [Run Hyper-V in a Virtual Machine with Nested Virtualization](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)
* [How to Run Docker on a Windows 10 Pro Hyper-V Virtual Machine](https://blog.michaeldeongreen.com/static-pages/how-to-run-docker-on-a-windows-10-pro-hyper-v-virtual-machine.html)
* [Azure Cosmos DB Emulator Docker Container](https://github.com/Azure/azure-cosmos-db-emulator-docker)
* [Docker Desktop - Chocolatey Package](https://chocolatey.org/packages/docker-desktop/2.3.0.4)
* [Enable-NestedVm PowerShell script](https://github.com/MicrosoftDocs/Virtualization-Documentation/blob/live/hyperv-tools/Nested/Enable-NestedVm.ps1)
* [Azure Cosmos DB Documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/)
* [Cosmos DB: Table API](https://docs.microsoft.com/en-us/azure/cosmos-db/table-introduction)
