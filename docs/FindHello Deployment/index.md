# Deployment Overview

## Web app

For the web application, deployment of both the frontend app and backend API is automated via CircleCI. This means that CircleCI listens to the Bitbucket repo, and when is sees a change on a repo branch, it automatically launches a new build. If that build is successful, it then deploys to the relevant server on AWS. This is set up and configured for both the `find-hello-app` and `find-hello-api` repos for both the `develop` and `master` branches.

## Mobile app

For the Android and iOS mobile apps, they need to be build and deployed manually. This means building local production/release versions of the application and then using the Google Play Console and Xcode + Apple App Store Connect console to upload the release builds.
