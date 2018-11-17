# lab-1

## Before lab-1

Before the student starts lab 1, do the following:

### Authenticate

As an instructor, sign into `gcloud` on the **student-workspace** server.

```
$ gcloud auth login --no-launch-browser
```

It will give you a URL to click.  Follow link and receive a token to enter back
into the SSH terminal to complete the login.

### Create a Service Account

Create a shared Google Cloud service account.  It will be in a folder that
everyone can use and access.

```
$ sudo mkdir -p /var/lib/gcloud
$ cd /var/lib/gcloud
$ sudo touch bob-the-builder.key.json
$ sudo chmod 0777 bob-the-builder.key.json
```

In this example, `bob-the-builder` is the name of the **service account** and
`bosh-operator-class` is our **project-id**.

```
$ gcloud iam service-accounts create bob-the-builder
$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' bob-the-builder.key.json
$ gcloud projects add-iam-policy-binding bosh-operator-class --member='serviceAccount:bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' --role='roles/editor'
```

If for any reason the key is lost, just use this command:

```
$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' bob-the-builder.key.json
```

And if too many keys get generated for the service account, manage them in the
IAM Admin [Service Account][service-account] page and delete unused keys.

### Ready

The student is now ready to begin student [lab-1][lab-1].

## Team Exercise Cheat Sheet

## Timers

Build times for specific actions.

  * bosh-lite create-env (10 mins)
  * google cloud zookeeper-deployment (10 mins)

### Deploy Steps

Here's the steps users need to be walked through:

1. Ensure users have updated cloud-config.
2. Clone the repo.
3. Fail bosh deploy.
4. Upload stemcell.
5. Success bosh deploy.

```
$ git clone https://github.com/cppforlife/zookeeper-release.git
$ bosh -d zookeeper deploy zookeeper-release/manifests/zookeeper.yml
let the user fail to deploy ...
then do the upload stemcell ...
... successful deploy
```

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/success.jpg" width="342" height="249" title="Success">

### Stemcell

Also could explore the [stemcell page][stemcell-page], talk about what it is, and how
to use the command.

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-google-kvm-ubuntu-trusty-go_agent
```

[lab-2][lab-2] awaits.

[//]: # (Links)

[stemcell-page]: http://bosh.cloudfoundry.org/stemcells/bosh-google-kvm-ubuntu-trusty-go_agent
[lab-1]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1
[lab-2]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-2
[service-account]: https://console.cloud.google.com/iam-admin/serviceaccounts/project
