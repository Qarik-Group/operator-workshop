# lab-1

bosh can deploy bosh.

![mind blown][mind-blown]

amirite?

## Environments

### Create Environment

1. Let's create a `create-env.sh` file.  

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
    -o bosh-deployment/external-ip-not-recommended.yml \
    -v director_name=bosh-director \
    -v internal_cidr=10.0.0.0/20 \
    -v internal_gw=10.0.0.1 \
    -v internal_ip=$MY_INTERNAL_IP \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=default \
    -v external_ip=$MY_EXTERNAL_IP
```

2. Set an `external_ip` value.

Before running the `create-env.sh` script see your student worksheet for the IP
addresses you'll use for the labs.

This command sets the environment variables in your SSH session.

```
export MY_INTERNAL_IP=10.0.0.6
export MY_EXTERNAL_IP=35.196.19.152
```

3. Clone the `bosh-deployment` repo to this folder.

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

What happens when we run this script?  Look at the error message for clues.

## Authentication

### Alias Environments

An alias creates a shortcut that saves the additional connection parameters to a name.  So if we had these parameters:

  * IP: 35.227.47.126
  * Certificate Authority
  * Name: bosh-director

In the same folder we ran the `create-env.sh`, we'd create an `alias-env` command like so:

```
$ bosh alias-env bosh-director -e 35.196.19.152 --ca-cert <(bosh int creds.yml --path /director_ssl/ca)
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

## Team Time

We're going to break into teams at this time.

You need to deploy a "hello world" BOSH release.  We recommended the
[zookeeper-release][zookeeper-release] for BOSH.

Every cloud needs a default **cloud-config** to begin.  The
[bosh-deployment][bosh-deployment-cloud-config] repo has example cloud-configs to get us
started.

First team to get all there team mates BOSH directors to a BOSH release, wins 10
points.  And the winning team at the end of our workshop gets a prize!

[bosh-deployment-cloud-config]: https://github.com/cloudfoundry/bosh-deployment#ops-files
[zookeeper-release]: https://github.com/cppforlife/zookeeper-release
[mind-blown]: https://github.com/starkandwayne/operator-workshop/raw/master/images/mind-blown.gif "Mind Blown"
