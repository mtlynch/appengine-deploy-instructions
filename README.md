# project-setup-instructions

This is just a collection of notes for setting up different projects on Google Cloud Project. They're mostly just for myself, but anyone reading this is welcome to use them as well.

## Setting up a new Go App Engine Standard project

1. Create new GCP project.
1. [Create a service account](https://console.cloud.google.com/iam-admin/serviceaccounts/create) specifically for deploying from CI:
1. Give it the following roles:
   - App Engine Admin
   - Cloud Build Editor
   - Storage Admin
1. Download service account key as JSON.
1. base64 encode JSON key: `cat service-account-creds.json | base64 --wrap=0 && echo ""`
1. Save the base64 encoded string as a CircleCI environment variable `CLIENT_SECRET`
1. [Enable AppEngine API](https://console.developers.google.com/apis/api/appengine.googleapis.com/overview)
1. Copy deployment config from [What Got Done](https://github.com/mtlynch/whatgotdone/blob/2fee6628d1057c47b27ce521fc7256ef29854358/.circleci/config.yml#L84-L114).
1. Change `GCLOUD_PROJECT` to the gcloud project ID for your project (not the project _name_).
1. From a dev machine, create the App Engine app for the project:
   1. `GCLOUD_PROJECT="gcp-project-name"`
   1. `gcloud auth login`
   1. `gcloud config set project $GCLOUD_PROJECT`
   1. `gcloud app create`
   1. `gcloud --quiet app deploy --promote`

## Setting up a new Firebase hosting project

1. Go to [Firebase console](https://console.firebase.google.com/).
1. Click "Add Project" and create the project.
1. Copy `.firebaserc` and `firebase.json` from an existing project (e.g., [hello-world-vue-static](https://github.com/mtlynch/hello-world-vue-static)).
   1. Customize the project ID in `.firebaserc`.
   1. Customize any rules in `firebase.json`.
1. Generate a firebase deploy token: `firebase login:ci`
1. Save the deploy token as a Circle CI environment variable: `FIREBASE_DEPLOY_TOKEN`
1. Copy deployment config from [hello-world-vue-static](https://github.com/mtlynch/hello-world-vue-static):
   1. [Persist the necessary files](https://github.com/mtlynch/hello-world-vue-static/blob/5d13fcf35a53328c9078a867dbce9a96cc927598/.circleci/config.yml#L14-L19) to the workspace.
   1. [Deploy them to firebase](https://github.com/mtlynch/hello-world-vue-static/blob/5d13fcf35a53328c9078a867dbce9a96cc927598/.circleci/config.yml#L20-L32).
1. (Optional): Configure a custom domain at Firebase > Hosting > Add custom domain.
