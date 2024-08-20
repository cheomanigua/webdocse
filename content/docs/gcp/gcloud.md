---
weight: 10100
title: "GCloud CLI"
description: ""
icon: "article"
date: "2024-08-13T10:44:42+02:00"
lastmod: "2024-08-13T10:44:42+02:00"
draft: false
toc: true
---
## Installation

```
$ curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-linux-x86_64.tar.gz
$ tar -xf google-cloud-cli-linux-x86_64.tar.gz
$ ./google-cloud-sdk/install.sh
```

The installation script will update automatically `.bashrc` to add cloud to the path. The path is set initially where the uncompressed directory is.


## Command utils
- **gcloud**: primary CLI tool to create and manage Google Cloud resource. [Info](https://cloud.google.com/sdk/gcloud/) | [Install](https://cloud.google.com/sdk/install) | [Initialize](https://cloud.google.com/sdk/docs/initializing)
- **beta**: Beta release level of **gcloud**. Commands are functionally complete, but may still have some outstanding issues. Breaking changes to these commands may be made without notice.
- **alpha**: Alpha release level of **gcloud**. Commands are in early release and may change without notice.
- **kubectl**: tool for controlling Kubernetes clusters. [[Info]](https://cloud.google.com/kubernetes-engine/docs/quickstart) | Installation: `gcloud components install kubectl`
- **gsutil**: Python application that lets you access **Cloud Storage** from the command line. [info](https://cloud.google.com/storage/docs/gsutil_install)
- **bq**: Enables running queries and manipulating datasets, tables, and entities in **BigQuery** through the command line. [Info](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-command-line)

**Note**: To manage your gcloud installation, you can see the commands available by typing `gcloud components`.

## General information
```
gcloud components update    // updates gcloud
gcloud init // first thing to do after installing gcloud
gcloud info

gcloud auth list
gcloud auth login // add a new gmail account 
gcloud auth login --no-launch-browser // add a new gmail account without automatic browser launch
gcloud config list // check active account, default project (could be from a different accout), and default services location
gcloud config set account john@doe.com // switch to specified account
gcloud config set project [PROJECT_ID] // switch to specified project
gcloud config set compute/zone [ZONE] // set default zone for gcloud
gcloud config set artifacts/location us-central1
gcloud iam service-accounts list

gcloud projects list
gcloud projects describe [PROJECT_ID]
gcloud projects get-ancestors [PROJECT_ID]
gcloud projects get-iam-policy [PROJECT_ID]
```

## Create a project
```
gcloud projects create [PROJECT_ID] --name="My Project"
gcloud beta billing accounts list
gcloud beta billing projects link [PROJECT_ID] --billing-account=0X0X0X-0X0X0X-0X0X0X
gcloud config set project [PROJECT_ID]
gcloud services list --available
gcloud services enable SERVICE_NAME
```
## Tip
After creating a project, set the created project as default so you don't need to specify the project in every command. Also, specify the zone or region for the same reason:
```
gcloud config set project [PROJECT_ID] // switch to specified project
gcloud config set compute/zone [ZONE] // set default zone for gcloud
```

## How to use multiple accounts efficiently

### Create a configuration

```
gcloud config configurations create [config-name]
gcloud config set project [project-id]
gcloud config set account [account]
gcloud config set compute/region [region]
gcloud config set compute/zone [zone]
```
**Note**: You can set less or more fields as you need

### Edit configuration
```
gcloud config configurations list  // list all configurations
gcloud config configurations activate [config-name]  // Switch to the configuration you want to edit
gcloud config set artifact/location [region]  // Edit the active configuration artifact region location
```

**Note**: You can set less or more fields as you need

### See configuration details
```
gcloud config configurations describe [config-name]
```

### Switch to a different configuration
```
gcloud config configurations activate [config-name]
```
