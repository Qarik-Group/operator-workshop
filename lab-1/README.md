# lab 1

## Create a Service Account

Let's create a Google Cloud service account.

```
$ gcloud iam service-accounts create bob-the-builder

$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@windy-ellipse-199217.iam.gserviceaccount.com' bob-the-builder.key.json

$ gcloud projects add-iam-policy-binding windy-ellipse-199217 --member='serviceAccount:bob-the-builder@windy-ellipse-199217.iam.gserviceaccount.com' --role='roles/editor'
```

## Setup Environment Variables

In this repo is an `env-variables` file.  It is set to defaults now of:

```
export BBL_GCP_SERVICE_ACCOUNT_KEY=~/operator-workshop/bob-the-builder.key.json
export BBL_IAAS=gcp
export BBL_GCP_REGION=us-west1
export BBL_ENV_NAME=lab-1
export BBL_DEBUG=1
```

If these look good you can load those variables in the Cloud Shell by "sourcing" the file like this:

```
$ . env-variables
```

## Install Tools

Run the `bin/install-tools` to install the `bosh-cli`, `terraform` and the `bosh bootloader` tools we're going to use.

From the `~/operator-workshop` folder run:

```
$ sudo bin/install-tools
```

Let's check that everything's up and running by checking that our lab plan can run.

```
$ bbl plan
```

If that works, run this to get setup the jumpbox.

```
$ bbl up
```
