# appengine-deploy-instructions

I'm writing this because I always forget the little gotchas for creating and deploying a new AppEngine app.

## Go Standard Environment

1. Create new GCP project
1. Create a service account specifically for deploying from CI
  1. Give it the following roles
    * App Engine Admin
    * Cloud Build Editor
    * Storage Admin
  1. Download service account key as JSON
1. base64 encode JSON key: `cat service-account-creds.json | base64 --wrap=0 && echo ""`
1. [Enable AppEngine API](https://console.developers.google.com/apis/api/appengine.googleapis.com/overview)
1. Copy deployment config from [What Got Done](https://github.com/mtlynch/whatgotdone/blob/2fee6628d1057c47b27ce521fc7256ef29854358/.circleci/config.yml#L84-L114)
