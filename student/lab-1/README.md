# lab-1

bosh can deploy bosh.

![mind blown][mind-blown]

amirite?

## Environments

### Create Environment

Let's create a `create-env.sh` file.  We do this because many times when we need to create a BOSH
director we will need to run or re-run this command.  And it's good to have it in a script.

This provides you with the ability to document what settings are used for your deployment, and to re-use the `create-env` command when performing updates to the BOSH director at a later time.

```
#!/usr/bin/env bash

set -eu

bosh create-env bosh-deployment/bosh.yml \
    --state=state.json \
    --vars-store=creds.yml \
    -o bosh-deployment/gcp/cpi.yml \
    -v director_name=bosh-1 \
    -v internal_cidr=10.0.0.0/24 \
    -v internal_gw=10.0.0.1 \
    -v internal_ip=10.0.0.6 \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=default
```

### Result

What is the result of running this script?  Why does it fail?

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
    -v director_name=bosh-1 \
    -v internal_cidr=10.142.0.0/24 \
    -v internal_gw=10.142.0.1 \
    -v internal_ip=10.142.0.6 \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=default
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
