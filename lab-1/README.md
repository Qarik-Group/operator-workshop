# lab 1

To begin all our labs, first clone the code repository from GitHub to your Google Cloud Shell environment.

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
$ cd operator-workshop
$ git submodule update --init --recursive
```

This will copy all our labs down to your Google Cloud Shell that we'll use today.

## Create a Service Account

Let's create a Google Cloud service account.

```
$ gcloud iam service-accounts create bob-the-builder

$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@windy-ellipse-199217.iam.gserviceaccount.com' bob-the-builder.key.json

$ gcloud projects add-iam-policy-binding windy-ellipse-199217 --member='serviceAccount:bob-the-builder@windy-ellipse-199217.iam.gserviceaccount.com' --role='roles/editor'
```

## Setup Environment Variables

Let's export our Service Account to the environment so we can use it to authenticate BOSH Bootloader commands.

```
$ export BBL_GCP_SERVICE_ACCOUNT_KEY=~/operator-workshop/bob-the-builder.key.json
```

Then we'll run this command to test that it's working right:

```
$ bbl plan --name lab-1 --iaas gcp --gcp-region us-west1
```

## Install Tools

Run the `bin/install-tools` to install the `bosh-cli`, `terraform` and the `bosh bootloader` tools we're going to use.

From the `~/operator-workshop` folder run:

```
$ bin/install-tools
```
