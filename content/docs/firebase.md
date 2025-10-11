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

#### What is Firebase

**Cloud Run** allows you to easily serve your backend code written in any programming language. **Firebase** adds a custom domain, a global content delivery network, and many more useful integrations. Together, Firebase and Cloud Run make it easy to build and deploy web apps that can handle large amounts of traffic.

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



## 3. Hugo site to GitHub to Firebase Hosting

If you don't want to install Firebase in your computer, you can create a CI/CD pipeline using GitHub Actions. This way when you `git push` in the project on your computer, GitHub Actions will automatically deploy the site on Firebase. These are the steps:

#### 1. Firebase account

1. Go to [Firebase console](https://console.firebase.google.com/)
2. Go to your project

##### Check your site ID

1. Click on the '**Project Settings**' gear icon on the left panel
2. Click on the '**General**' tab
3. If there is only one site on your project, the **Project ID** is your **Site ID**.

##### Generate a secret key

1. Click on the '**Project Settings**' gear icon on the left panel
2. Click on the '**Service accounts**' tab
3. Click on **Generate a new private key**
4. A **.json** key file will be downloaded into your computer

#### 2. Your computer

1. Convert the JSON key file to a base64-encoded string to store it safely in GitHub Secrets:
	```bash
	$ base64 yourprojectid-firebase-adminsdk-alphanum-alphanum.json | tr -d '\n'
	```
2. Copy the output to the clipboard

#### 3. GitHub account

##### Add secret key for Google Service Account

1. Go to your repository -> Settings -> Secrets and variables -> Actions
2. Click on **New repository secret**
3. Paste the output of the clipboard in the box **Secret**
4. Name your new secret `GOOGLE_APPLICATION_CREDENTIALS` in the box **Name**

##### Add secret for your Site ID

1. Click on **New repository secret**
2. Write the **Project ID** or **Site ID** in the box **Secret**
3. Name your new secret `FIREBASE_PROJECT_ID` in the box **Name**

You will use `GOOGLE_APPLLICATION_CREDENTIALS` and `FIREBASE_PROJECT_ID` when creating the `deploy.yml` file below.

#### 4. Your computer

##### Create `firebase.json` file

In your root project directory, create the file **firebase.json** with the following content, making sure that you type your site ID and not your site URL:

```json
{
  "hosting": {
    "public": "public",
    "site": "my_site_id",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**",
      ".git/",
      ".gitignore",
      "README.md"
    ]
  }
}
```

##### Create `.deploy.yml` file

- Create the folling directory at the root of your project directory: `.github/workflow/`
- Inside the new created directory, create the file **deploy.yml** with the following content:

```yml
name: Deploy to Firebase Hosting
on:
  push:
    branches:
      - main
jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive  # Ensure theme submodules are checked out
      - name: Install Hugo Extended
        run: |
          wget https://github.com/gohugoio/hugo/releases/download/v0.131.0/hugo_extended_0.131.0_Linux-64bit.tar.gz
          tar -xzf hugo_extended_0.131.0_Linux-64bit.tar.gz
          mv hugo /usr/local/bin/
      - name: Build Hugo
        run: hugo --minify
      - name: Install Firebase CLI
        run: npm install -g firebase-tools
      - name: Set up Google Service Account
        run: |
          echo "${{ secrets.GOOGLE_APPLICATION_CREDENTIALS }}" | base64 -d > $HOME/gcp_credentials.json
          export GOOGLE_APPLICATION_CREDENTIALS=$HOME/gcp_credentials.json
          echo "GOOGLE_APPLICATION_CREDENTIALS=$HOME/gcp_credentials.json" >> $GITHUB_ENV
      - name: Deploy to Firebase
        run: firebase deploy --only hosting --project ${{ secrets.FIREBASE_PROJECT_ID }}
```

##### Push the new changes to GitHub

```bash
$ git add .
$ git commit -m 'Created CI/CD pipeline with GitHub Actions'
$ git push
```
{{< alert text="I'm using `hugo_extended_0.131.0_Linux-64bit.tar.gz`. because it's the only version of Hugo that builds for the template [Lotus Docs].(https://lotusdocs.dev/docs/)" />}}

# Deleting

1. In your local computer, go to your project's directory and delete the following files and directory: `firebase.json`, `.firebaserc` and `.firebase/`
2. If you also want to delete the project in the Firebase web console:
    1. Go to your [Firebase console](https://console.firebase.google.com/)
    2. Select the project you want to delete
    3. Go to the [Project settings](https://console.firebase.google.com/project/_/settings/general/)
    4. At the bottom of the **General** settings page, click **Delete project**
    5. Select each checkbox to acknowledge the effects of deleting your project
    6. Click **Delete project**



