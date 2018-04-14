# lab-1

## Before lab-1

Before the student starts lab 1, do the following:

### Authenticate

As an instructor, sign into `gcloud` on the **student-workspace** server.

```
$ gcloud auth login
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

## Team Up Cheat Sheet

### Release Steps

Then these commands would be able to help them do a deploy.

1. Ensure users have updated cloud-config.
2. Clone the repo.
3. Deploy bosh release.

```

$ git clone https://github.com/cppforlife/zookeeper-release.git
$ cd zookeeper-release
$ bosh -d zookeeper deploy manifests/zookeeper.yml
... fail
upload stemcell
... succeed
```

### Stemcell

Yet they will also need to upload a stemcell.  Let them fail on that first.
Then explore the [stemcell page][stemcell-page], talk about what it is, and how
to use the command.

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-google-kvm-ubuntu-trusty-go_agent
```

[//]: # (Links)

[stemcell-page]: http://bosh.cloudfoundry.org/stemcells/bosh-google-kvm-ubuntu-trusty-go_agent
[lab-1]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1
[service-account]: https://console.cloud.google.com/iam-admin/serviceaccounts/project
