# Firebase deploy

> Docker images including the necessary tools to deploy your apps to Firebase

[![Docker Automated build](https://img.shields.io/docker/automated/kawatta/firebase-deploy.svg)](https://hub.docker.com/r/kawatta/firebase-deploy/)
[![Docker Build Status](https://img.shields.io/docker/build/kawatta/firebase-deploy.svg)](https://hub.docker.com/r/kawatta/firebase-deploy/)
[![Docker image size](https://img.shields.io/microbadger/image-size/kawatta/firebase-deploy.svg)](https://hub.docker.com/r/kawatta/firebase-deploy/)

## Goal

This image was created to allow interacting with [Firebase](https://github.com/firebase/firebase-deploy) from a CI/CD environment (e.g. publish a website to Firebase).

## Images

### alpine
This the recommended image if you are only using [Firebase hosting](https://firebase.google.com/docs/hosting/), for example to host a static website.
This image uses Linux Alpine.

### stretch
This the recommended image if:
- you are using services from Firebase other than hosting, like [Cloud functions for Firebase](https://firebase.google.com/docs/functions/).
- your application requires building [native modules](https://nodejs.org/api/addons.html) or you need a dependency not available for [Alpine](https://pkgs.alpinelinux.org/packages?name=&branch=v3.8). 

## Usage example

### Gitlab CI + Firebase hosting:
```yml
# Inside .gitlab-ci.yml
# The environment variables FIREBASE_PROJECT and FIREBASE_TOKEN need to be declared in the CI/CD settings of your project

image: kawatta/firebase-deploy:alpine

cache:
  paths:
    - node_modules/ # caching npm modules will allow subsequent builds to run faster, if your project relies on Node 

before_script:
  - npm install # This is only needed if you need Node to build your assets

deploy-to-firebase:
  stage: deploy
  script:
  - npm run build # Building assets
  - firebase use $FIREBASE_PROJECT --token $FIREBASE_TOKEN
  - firebase deploy --only hosting -m "Build $CI_JOB_ID Commit $CI_COMMIT_SHA" --token $FIREBASE_TOKEN
  only:
  - master
```

### Gitlab CI + Firebase hosting and functions:
```yml
# Inside .gitlab-ci.yml
# The environment variables FIREBASE_PROJECT and FIREBASE_TOKEN need to be declared in the CI/CD settings of your project

image: kawatta/firebase-deploy:stretch

cache:
  paths:
    - node_modules/ # caching npm modules will allow subsequent builds to run faster, if your project relies on Node 

before_script:
  - npm install # This is only needed if you need Node to build your assets

deploy-to-firebase:
  stage: deploy
  script:
  - npm run build # Building assets
  - firebase use $FIREBASE_PROJECT --token $FIREBASE_TOKEN
  - firebase deploy -m "Build $CI_JOB_ID Commit $CI_COMMIT_SHA" --token $FIREBASE_TOKEN
  only:
  - master
```
