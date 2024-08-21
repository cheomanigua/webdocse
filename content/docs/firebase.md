---
weight: 500
title: "Firebase"
description: ""
icon: "article"
date: "2024-08-13T11:25:53+02:00"
lastmod: "2024-08-13T11:25:53+02:00"
draft: false
toc: true
---

# Basics

## Installation

```
$ curl -sL https://firebase.tools | bash
```

## Update
```
$ curl -sL https://firebase.tools | upgrade=true bash
```

## Authentication
```
$ firebase login                            // first time login after installation
$ firebase login:add another@email.com      // add another account
$ firebase login:use this@email.com         // switch between accounts
```

## Commands

The following commands are not global. They only affect the directory where the project is present.

```
firebase init hosting           // set up a hosting service
firebase init hosting:github    // set up github connection if it was not set during 'firebase init hosting'
firebase projects:list
firebase hosting:channel:list                           // List all Preview Channels
firebase hosting:channel:deploy stage --expires 2d      // Create a Preview Channel called stage
firebase hosting:channel:delete stage                   // Delete a Preview Channel called stage 
firebase use                    // View a list of currently defined aliases for your project directory 
firebase use --add              // Add another Firebase project to run commands from the current project directory 
firebase use --clear            // Clears the active project
```

# Deploying

## 1. Hugo site to Firebase Hosting (static site)

1. Go to [Firebase](https://console.firebase.google.com) and create a new project
2. In your local machine, in the root directory of your Hugo project, type: `$ firebase init hosting`
    - **? Please select an option:** '*> Use an existing project*' enter
    - **? Select a default Firebase project for this directory:** up/down cursor + enter
    - **? What do you want to use as your public directory? *public*** enter
    - **? Configure as a single-page app (rewrite all urls to /index.html)? *(y/N)*** enter
    - **? Set up automatic builds and deploys with GitHub? *(y/N)*** enter 
    - **? File public/404.html already exists. Overwrite? *(y/N)*** enter (if applicable)
    - **? File public/index.html already exists. Overwrite? *(y/N)*** enter (if applicable)

3. In your local machine, in the root directory of your Hugo project, type: `$ hugo && firebase deploy`

### GitHub
There is not CI/CD process here. Pushing to your GitHub repository will only update your GitHub repository, it won't affect the current Hugo deployment. 

So, if you want to update your deployment with the last code changes, you have to run `hugo && firebase deploy`


## 2. Cloud Run app to Firebase Hosting (dynamic site)

1. Access your GCP [console](https://console.cloud.google.com)
2. Go to [Cloud Run](https://console.cloud.google.com/run)
3. Click on '**Manage Custom Domains**' button
4. Click on '**Add mapping**' button
5. From the drop down menu, select a service to map to
6. Click on '**Firebase Hosting**'
7. Type in the subdomain name (must be available)
8. If not set yet, click on '**Grant All**' button to grant the necessary permissions to deploy the integration
9. If not set yet, click on '**Enable**' to enable the required APIs
10. Click on the '**Submit**' button

### Use case

**Cloud Run** allows you to easily serve your backend code written in any programming language. **Firebase** adds a custom domain, a global content delivery network, and many more useful integrations. Together, Firebase and Cloud Run make it easy to build and deploy web apps that can handle large amounts of traffic.


# Deleting

1. In your local computer, go to your project's directory and delete the following files and directory: `firebase.json`, `.firebaserc` and `.firebase/`
2. If you also want to delete the project in the Firebase web console:
    1. Go to your [Firebase console](https://console.firebase.google.com/)
    2. Select the project you want to delete
    3. Go to the [Project settings](https://console.firebase.google.com/project/_/settings/general/)
    4. At the bottom of the **General** settings page, click **Delete project**
    5. Select each checkbox to acknowledge the effects of deleting your project
    6. Click **Delete project**

# CI/CD

## Failing to setup CI/CD (ignore all instructions below)

## Deploy Hugo site with GitHub

- Use an existing repository or create a new one
- Within the root directory of the web project, type: `$ firebase init hosting`
- **? Select a default Firebase project for this directory:** cursor keys + enter
- **? What do you want to use as your public directory? *public*** enter
- **? Configure as a single-page app (rewrite all urls to /index.html)? *(y/N)*** enter
- **? Set up automatic builds and deploys with GitHub? *(y/N)*** y + enter 
- **? For which GitHub repository would you like to set up a GitHub workflow? *(format: user/repository)*** user/repository + enter
- **? Set up the workflow to run a build script before every deploy? *(y/N)*** enter
- **? Set up automatic deployment to your site's live channel when a PR is merged? *(Y/n)*** enter
- **? What is the name of the GitHub branch associated with your site's live channel? *(main)*** enter

**PR** is the acronym for **Pull Request**

### GCP service account

In order to automaticaly create and active the GCP services account necessary for your project, follow these steps:
1. From your [Firebase console](https://console.firebase.google.com/), go to your project
2. Click on the 'Project Settings' gear icon on the left panel
3. Click on the '**Service accounts**' tab
4. Click on the '**4 service accounts**' link besides the GCP icon

## Preview Channel

Firebase Hosting has a feature called **Preview Channels** where you can deploy a verion of your site to a generated short lived URL. They are great in a GitHub workflow.

You can setup a GitHuB action that will deploy a Preview Channel for every single Pull Request (PR). Firebase CLI will generate the entire workflow for you.

## Setup Preview Channel with GitHub Actions

- Push a Pull request per branch, and when it happens, generate a preview channel per pull request.
```
$ git branch next
$ git checkout next
$ git status
$ git add .
$ git commit -m 'adding Firebase Hosting'
$ git push -u origin next
```
- Go to your GitHub repository and click on the **Compare & pull request** button
- If you are happy with the default pull request name, click on **Create pull request**
