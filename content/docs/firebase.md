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

**Firebase** adds a custom domain, a global content delivery network, and many more useful integrations to Cloud Run. **Cloud Run** allows you to easily serve your backend code written in any programming language. Together, Firebase and Cloud Run make it easy to build and deploy web apps that can handle large amounts of traffic.

# Basics

## Installation

**Note**: You only need to install Firebase if you are deploying from your local computer as explained in the section *1. Hugo site to Firebase Hosting (static site)* in the *Deploying* section [below](#1-hugo-site-to-firebase-hosting-static-site)

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

In this section I will explain three different ways to deploy your app to Firebase.

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

You will use `GOOGLE_APPLICATION_CREDENTIALS` and `FIREBASE_PROJECT_ID` when creating the `deploy.yml` file below.

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



# Docker/Podman

You can create a Firebase development environment in your local machine by firing up the Firebase Emulator suite container. Bad news is that there is no official image. Good news is that you can create one.


### 1. Create the environment

Firebase Emulator needs a minimal environment to run propefly composed of five files: Containerfile, podman-compose.yaml, firebase.json, firestore.rules and firestore.indexes.json.

Below is the structure of the project and the content of the files:

```
Project Root
│
├─Containerfile
├─podman-compose.yaml
├─firebase.json
├─firestore.rules
├─firestore.indexes.json
│
└─public/
  └─index.html
```

- ##### `Containerfile`

```docker
# Stage 1: Builder
FROM node:20-slim AS builder

RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-17-jre-headless \
    ca-certificates \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN npm install -g firebase-tools@13.25.0

WORKDIR /workspace
COPY firebase.json .
COPY firestore.rules .
COPY firestore.indexes.json .
COPY public ./public

# Stage 2: Final Image
FROM node:20-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-17-jre-headless \
    ca-certificates \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN npm install -g firebase-tools@13.25.0

WORKDIR /workspace
COPY --from=builder /workspace /workspace

EXPOSE 4000 5000 8080 9099

ENTRYPOINT ["firebase"]

CMD ["emulators:start","--project=my-test-project","--only=auth,firestore,hosting,ui"]
```

- ##### `podman-compose.yaml`

```yaml
version: "3.9"

services:
  firebase:
    image: localhost/firebase-emulator:latest
    container_name: firebase-emulator

    working_dir: /workspace

    # Required for Fedora/RedHat/SELinux!
    security_opt:
      - label=disable

    volumes:
      - ./firebase.json:/workspace/firebase.json:Z
      - ./firestore.rules:/workspace/firestore.rules:Z
      - ./firestore.indexes.json:/workspace/firestore.indexes.json:Z
      - ./public:/workspace/public:Z
      - ./.firebase_data:/workspace/.firebase_data:Z

    ports:
      - "4000:4000"   # Emulator UI
      - "5000:5000"   # Hosting
      - "8080:8080"   # Firestore
      - "9099:9099"   # Auth

    command:
      [
        "emulators:start",
        "--project=my-test-project",
        "--only=auth,firestore,hosting,ui",
      ]
```

- ##### `firebase.json`

```json
{
  "hosting": {
    "public": "public",
    "host": "0.0.0.0"
  },
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json",
    "host": "0.0.0.0"
  },
  "emulators": {
    "hosting": { "port": 5000, "host": "0.0.0.0" },
    "firestore": { "port": 8080, "host": "0.0.0.0" },
    "auth": { "port": 9099, "host": "0.0.0.0" },
    "ui": { "enabled": true, "port": 4000, "host": "0.0.0.0" }
  }
}
```

- ##### `firestore.rules`

```
// Allow read/write on the emulator for dev
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

- ##### firestore.indexes.json

```json
{
  "indexes": [],
  "fieldOverrides": []
}
```

### 2. Build the image

Now run:

```bash
$ podman build -t firebase-emulator .
```

After a while, the image will be created. You can check:

```bash
$ podman images

REPOSITORY                   TAG          IMAGE ID      CREATED         SIZE
localhost/firebase-emulator  latest       93599db124d7  57 seconds ago  617 MB
```

### 3. Create the container

- In order to create the container, run:

```bash
$ podman compose up -d
```

- Check that the container is created and running:

```bash
$ podman ps

CONTAINER ID  IMAGE                               COMMAND               CREATED         STATUS         PORTS                                                                                           NAMES
758e03c05eb9  localhost/firebase-emulator:latest  emulators:start -...  40 minutes ago  Up 41 minutes  0.0.0.0:4000
```

- To stop and delete the container:

```bash
$ podman compose down
```

When the container is running, you can access the Firebase Emulator in these ports:

| Port | Service |
| --- | ---
| localhost:4040 | emulator ui |
| localhost:5000 | hosting |
| localhost:8080 | firestore |
| localhost:9099 | auth |
