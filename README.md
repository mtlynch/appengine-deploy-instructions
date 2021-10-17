# project-setup-instructions

This is just a collection of notes for setting up different projects on Google Cloud Project. They're mostly just for myself, but anyone reading this is welcome to use them as well.

## Setting up a new Go App Engine Standard project

```bash
PROJECT_ID="TODO"
BILLING_ACCOUNT="TODO"
SERVICE_ACCOUNT_NAME="ci-deployer"
REGION=us-east1

gcloud projects create "${PROJECT_ID}"
gcloud config set project "${PROJECT_ID}"
gcloud alpha billing projects link \
    "${PROJECT_ID}" \
    --billing-account "${BILLING_ACCOUNT}"
gcloud services enable appengine.googleapis.com cloudbuild.googleapis.com
gcloud app create --region="${REGION}"
SERVICE_ACCOUNT_EMAIL="${SERVICE_ACCOUNT_NAME}@${PROJECT_ID}.iam.gserviceaccount.com"
gcloud iam service-accounts create "${SERVICE_ACCOUNT_NAME}"

ROLES=('appengine.appAdmin' 'storage.admin' 'cloudbuild.builds.editor' 'iam.serviceAccountUser')
# If we need cron.yaml
ROLES+=('cloudscheduler.admin')
for ROLE in "${ROLES[@]}"
do
  gcloud projects add-iam-policy-binding \
      "${PROJECT_ID}" \
      --member="serviceAccount:${SERVICE_ACCOUNT_EMAIL}" \
      --role="roles/${ROLE}"
done

mkdir .circleci
pushd .circleci
wget https://raw.githubusercontent.com/mtlynch/whatgotdone/master/.circleci/config.yml
popd

CLIENT_SECRET=$(mktemp)

gcloud iam service-accounts keys create "${CLIENT_SECRET}" \
    --iam-account="${SERVICE_ACCOUNT_EMAIL}" \
    --key-file-type=json

cat "${CLIENT_SECRET}" | base64 --wrap=0 && echo ""
```

1. Set the billing account ID.
1. Change `GCLOUD_PROJECT` to the gcloud project ID for your project (not the project _name_).

## Setting up a new Cloud Functions project

Same as above, but the services line is:

```bash
gcloud services enable cloudbuild.googleapis.com cloudfunctions.googleapis.com
```

And the roles line is:

```bash
ROLES=('appengine.appAdmin' 'cloudbuild.builds.builder' 'cloudbuild.builds.editor' 'cloudfunctions.admin' 'iam.serviceAccountUser' 'run.invoker' 'storage.admin' 'storage.objectAdmin')
```

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
