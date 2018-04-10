# lab 1

## Pre-student

Before the student starts lab 1, do the following:

### Authenticate

As an instructor, sign into `gcloud` on the **student-workspace** server.

```
gcloud auth login
```

It will give you a URL to click.  Follow link and receive a token to enter back into the SSH terminal to complete the login.

### Create a Service Account

Create a shared Google Cloud service account.  It will be in a folder that everyone can use and access.

```
$ sudo mkdir -p /var/lib/gcloud
$ cd /var/lib/gcloud
$ sudo touch bob-the-builder.key.json
$ sudo chmod 0777 bob-the-builder.key.json
```

In this example, `bob-the-builder` is the name of the **service account** and `bosh-operator-class` is our **project-id**.

```
$ gcloud iam service-accounts create bob-the-builder
$ gcloud iam service-accounts keys create --iam-account='bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' bob-the-builder.key.json
$ gcloud projects add-iam-policy-binding bosh-operator-class --member='serviceAccount:bob-the-builder@bosh-operator-class.iam.gserviceaccount.com' --role='roles/editor'
```

If for any reason the key is lost, the middle command `gcloud iam service-accounts keys create` can be used to re-generate the key.

### Ready

The student is now ready to begin lab-1.

## During lab-1

### IP Out of Range

When users do a `bosh create-env` and for the network settings we have some defaults.

```
us-east1	default		10.142.0.0/20	10.142.0.1			Flow logs:Off
```

Yet, the first time they to the `bosh create-env` they get and error that the internal IP is outside the range.

```
Deploying:
  Creating instance 'bosh/0':
    Creating VM:
      Creating vm with stemcell cid 'stemcell-3eed4c18-94fb-490d-6841-88c09c1bb9d3':
        CPI 'create_vm' method responded with error: CmdError{"type":"Bosh::Clouds::VMCreationFailed","message":"VM failed to create: googleapi: Error 400: Invalid
value for field 'resource.networkInterfaces[0].networkIP': '10.0.0.6'. Requested internal IP is outside the subnetwork CIDR range., invalid","ok_to_retry":true}
Exit code 1
```

To fix this, they need to *change the IP* to a `10.142.x.x` address.
