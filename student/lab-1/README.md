# lab-1

bosh can deploy bosh.

![mind blown][mind-blown]

amirite?

## Environments

### Create Environment

1. Let's create a `create-env.sh` file in your `lab-1` folder.

We do this because many times when we need to create a BOSH director we will
need to run or re-run this command.  And it's good to have it in a script.

This provides you with the ability to document what settings are used for your
deployment, and to re-use the `create-env` command when performing updates to
the BOSH director at a later time.

```
#!/usr/bin/env bash

set -eu

bosh create-env bosh-deployment/bosh.yml \
    --state=state.json \
    --vars-store=creds.yml \
    -o bosh-deployment/gcp/cpi.yml \
    -o bosh-deployment/uaa.yml \
    -o bosh-deployment/credhub.yml \
    -o bosh-deployment/external-ip-not-recommended.yml \
    -v director_name=bosh-director \
    -v internal_cidr=$MY_CIDR \
    -v internal_gw=$MY_GW \
    -v internal_ip=$MY_INTERNAL_IP \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=student \
    -v subnetwork=$MY_SUBNET \
    -v external_ip=$MY_EXTERNAL_IP
```

2. Set an `external_ip` value.

Before running the `create-env.sh` script see your student worksheet for your
X = to your Student ID, and the IP addresses you'll use for the labs.

These commands set the environment variables for your IP addresses in your
SSH session.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
```

3. Now you'll clone the `bosh-deployment` repo to this folder.

```
$ git clone https://github.com/cloudfoundry/bosh-deployment
```

This provides us with the software we need to deploy BOSH.

4. We're now ready to test the `create-env.sh` script and see what we get.

```
$ sudo chmod +x create-env.sh
$ ./create-env.sh
```

### Result

NOTE: What happens as we deploy bosh?  What does each stage of the process mean?

## Authentication

### Alias Environments

An alias creates a shortcut that saves the additional connection parameters to a name.  So if we had these parameters:

  * IP: 35.227.47.126
  * Certificate Authority
  * Name: bosh-director

In the same folder we ran the `create-env.sh`, we'd create an `alias-env` command like so:

```
$ bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int creds.yml --path /director_ssl/ca)
```

### Using Environments

When using bosh commands we can either provide the environment in each command,
for example:

```
$ bosh -e bosh-director stemcells
$ bosh -e bosh-director update-cloud-config
$ bosh -e bosh-director deployments
```

Or you can export an environment variable, and the commands will look like this.

```
$ bosh stemcells
$ bosh update-cloud-config
$ bosh deployment
```

Here's how we do this:

```
export BOSH_ENVIRONMENT=bosh-director
```

NOTE: How do you check what your environment is set to?

### Login to BOSH

To get the admin password for login, BOSH created it for us while the `create-env` command was run.  It was rendered to the `creds.yml` just like the CA cert was.

Let's open the file to look at it.

```
$ less creds.yml
```

To login to the BOSH director, copy the admin password from the `creds.yml`, then run the `login` command.  The username is `admin`.

```
$ bosh login
```

### Result

We are now logged into the director.

BONUS: How could you do a `bosh login` with something like a
`bosh int creds.yml --path /admin_password` to make a one-line login?

## Cloud Config

In order to deploy, BOSH needs default configuration for the cloud it's working
with.  The [bosh-deployment][bosh-deployment-cloud-config] repo has example
cloud-configs for each cloud.

We have one prepared in our `lab-1` folder here.  Take a look at it now.

In order to set this configuration on our newly created BOSH director, we use
the `bosh update-cloud-config` command.

Yet we're going to want to pass in some variables like this:

```
$ bosh update-cloud-config cloud-config.yml -v internal_cidr=$MY_CIDR -v internal_gw=$MY_GW -v subnetwork_name=$MY_SUBNET
```

## Team Up

We're going to break into teams at this time.

You need to deploy a "hello world" BOSH release.  We recommended the
[zookeeper-release][zookeeper-release] for BOSH.

First team to get all their teammates to deploy the BOSH release, wins 10
points.  At the end of the workshop we have a prize for the winning team!

[bosh-deployment-cloud-config]: https://github.com/cloudfoundry/bosh-deployment#ops-files
[zookeeper-release]: https://github.com/cppforlife/zookeeper-release
[mind-blown]: https://github.com/starkandwayne/operator-workshop/raw/master/images/mind-blown.gif "Mind Blown"
[lab-2]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-2
