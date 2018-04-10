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
    -v internal_cidr=10.0.0.0/24 \
    -v internal_gw=10.0.0.1 \
    -v internal_ip=10.0.0.6 \
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
address you'll use for the labs.

This command sets the environment variable in your SSH session, where
`35.196.19.152` is your IP address.

```
export MY_EXTERNAL_IP=35.196.19.152
```

3. Clone the `bosh-deployment` repo to this folder.

```
$ git clone https://github.com/cloudfoundry/bosh-deployment
```

This provides us with the software we need to deploy BOSH.

4. We're now ready to test the `create-env.sh` script and see what we get.

```
$ ./create-env.sh
```

### Result

What is the result of running this script?  Why does it fail?

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

NOTE: Why do we want to run it there?

Then we're going to set an environment variable to the name of our alias.

```
export BOSH_ENVIRONMENT=bosh-director
```

NOTE: What does this save us?

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

### Add Processes

Another great advantage of having a `create-env.sh` script is that you can add Processes
(services) to your BOSH director with operator files.

In this example we'll add the UAA and CredHub processes to our BOSH Director.

```
#!/usr/bin/env bash

set -eu

bosh create-env bosh-deployment/bosh.yml \
    --state=state.json \
    --vars-store=creds.yml \
    -o bosh-deployment/gcp/cpi.yml \
    -o bosh-deployment/external-ip-not-recommended.yml \    
    -v director_name=bosh-director \
    -v internal_cidr=10.142.0.0/24 \
    -v internal_gw=10.142.0.1 \
    -v internal_ip=10.142.0.6 \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=default \
    -v external_ip=35.196.19.152
```

TODO: add the flags for UAA and CredHub.

### Result

How do we check for UAA and Credhub after deploying them?

### Delete-env

Delete env stands as an inverse to `create-env` and is used in pre-production
environments.  It takes the same arguments and state file

### Result

Our BOSH Director is gone.  But we can get it back by running create-env at any
time, more quickly this next time because resources for the releases have been
downloaded and compiled locally already.

If you lose the state file it can be recovered only if you can determine specific
variables from your infrastructure and regenerate it by using the `create-env`
command.
https://bosh.io/docs/cli-envs.html#recover-deployment-state

[mind-blown]: https://github.com/starkandwayne/operator-workshop/raw/master/images/mind-blown.gif "Mind Blown"
