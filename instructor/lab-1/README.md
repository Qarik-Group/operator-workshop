# lab 1

For the instructor, this is what you'll do to help students be ready to do lab 0.

## Authenticate

As an instructor, sign into the **student-workspace** server.

```
gcloud auth login
```

It will give you a URL to click.  Follow link and receive a token to enter back into the SSH terminal to complete the login.

## Create a Service Account

Let's create a Google Cloud service account.  We're going to create a universal folder and file that every student will use a shared service account.  It will be in `/var/lib/gcloud` and the `bob-the-builder.key.json` file.

Create the folder and file with permissions that any user can get to.

```
$ sudo mkdir -p /var/lib/gcloud
$ cd /var/lib/gcloud
$ touch bob-the-builder.key.json
$ sudo chmod 0777 bob-the-builder.key.json
```

Run these `gcloud` commands to create the service account, where `bob-the-builder` is the name of the service account and `bosh-operator-class` is our project-id.

```
$ gcloud iam service-accounts create bob-the-builder
$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' bob-the-builder.key.json
$ gcloud projects add-iam-policy-binding bosh-operator-class --member='serviceAccount:bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' --role='roles/editor'
```

## Setup Environment Variables

In this repo is an `env-variables` file.  It's defaults are set to:

```
export BBL_GCP_SERVICE_ACCOUNT_KEY=/var/lib/gcloud/bob-the-builder.key.json
export BBL_IAAS=gcp
export BBL_GCP_PROJECT_ID=bosh-operator-class
export BBL_GCP_REGION=us-east1
export BBL_GCP_ZONE=us-east1-b
export BBL_ENV_NAME=lab-1
export BBL_DEBUG=1
```

If these look good you can load those variables on the **student-workspace** server by "sourcing" the file:

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

[mind-blown]: https://github.com/starkandwayne/operator-workshop/raw/master/images/mind-blown.gif "Mind Blown"
