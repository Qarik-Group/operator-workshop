# lab-1

bosh can deploy bosh.

![mind blown][mind-blown]

amirite?

## Environments

### Create Environment

Let's make sure we start in your `lab-1` folder:

```
$ cd ~/operator-workshop/student/lab-1
```

1. Manage and set environment variables.

Each of you will have received a card with the values to the environment
variables below.  If not, contact an instructor now.

Since there are a number of environment variables to manage we're going to
create a `set-vars` file in our home directory.

```
$ vi ~/set-vars
```

2. `vi` needs to be in "insert mode" before we can add text.  Push the `i` key
to do this, and you'll be able to insert the following to the file:

```
# variables

export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
export MY_ZONE=us-east1-c

# output
echo "MY_CIDR=$MY_CIDR"
echo "MY_GW=$MY_GW"
echo "MY_INTERNAL_IP=$MY_INTERNAL_IP"
echo "MY_EXTERNAL_IP=$MY_EXTERNAL_IP"
echo "MY_SUBNET=$MY_SUBNET"
echo "MY_ZONE=$MY_ZONE"
```

To save a file with `vi`, hit the `ESC` key and then type `:wq` and ENTER.

Now in future labs we can source these environment variables at any time by
running:

```
$ . ~/set-vars
```

3. Next, let's create a `create-env.sh` file in your `lab-1` folder.  Use  `vi`
again.

```
$ vi create-env.sh
```

Remember, press `i` to start the insert process:

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
    -v zone=$MY_ZONE \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=$MY_SUBNET \
    -v external_ip=$MY_EXTERNAL_IP
```

Then hit `ESC`, `:wq`, ENTER to save and quit.

If you have any problems getting this file created and saved please get help
from an instructor on-site.

Why do we create this file?  Because many times we don't just need to create a
BOSH director once, but we may need to run or re-run the command because we
may be missing a dependency or two, and so it's good to have it in a script.

Also, this provides you with the ability to document what settings were used
when your deployed your BOSH director.  OR if you need to add something new
to your existing BOSH director, it's already correctly configured and ready
to go.

4. Now we'll clone the `bosh-deployment` repo to this folder.

```
$ git clone https://github.com/cloudfoundry/bosh-deployment
```

This provides us with the software we need to deploy BOSH.

5. We're now ready to run the `create-env.sh` script and use `bosh` to deploy a
bosh-director.

```
$ sudo chmod +x create-env.sh
$ ./create-env.sh
```

This process should take about 10 - 12 minutes.

### Result

NOTE: What happens as we deploy bosh?  What does each stage of the process mean?

## Authentication

Before we can do more with our BOSH director, we need to sign into it.  And
during the deployment process, a default password was generated in our `lab-1`
folder in the `creds.yml` file.

Here's the process used to sign into a BOSH director.

### Alias Environments

An alias creates a shortcut that saves connection parameters to a name.

In our `lab-1` folder, run:

```
$ bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int creds.yml --path /director_ssl/ca)
```

Now we have an environment alias called `bosh-director` that points to
our external IP and can supply our SSL certificate.

### Login to BOSH

When we created the BOSH director, it generated a default password for us.
This is configured in the `--vars-store=creds.yml` flag during the
`create-env` command.

Therefore we can look at the `creds.yml` file, for what password was
generated.

```
$ less creds.yml
```

To login to the BOSH director, copy the value from `admin_password:` from
the `creds.yml`.  Then run this `bosh` command:

```
$ bosh -e bosh-director login
```

We should now be authenticated to the BOSH director.  Please raise your hand and
get help from an instructor if you need assistance with this.  Thank you!

### Result

We are now logged into the director.

BONUS: How could you do a `bosh login` with something like a
`bosh int creds.yml --path /admin_password` to make a one-line login?

### Using Environments

When using bosh commands we can either provide the environment alias
in each command:

```
$ bosh -e bosh-director stemcells
$ bosh -e bosh-director update-cloud-config
$ bosh -e bosh-director deployments
```

Or you can export an environment variable, and the commands look like this.

```
$ bosh stemcells
$ bosh update-cloud-config
$ bosh deployment
```

Simply export your environment alias as the `BOSH_ENVIRONMENT` variable value.

```
export BOSH_ENVIRONMENT=bosh-director
```

NOTE: How do you check what your environment is set to?

## Cloud Config

The cloud-config provides default configs for each deployment to use, that
unless overridden, will provide a smooth working experience to deploy
software on the infrastructure.

The [bosh-deployment][cloud-config] repo has example `cloud-configs` for
each major cloud like AWS, GCP, Azure, and so on.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/simple-cloud-config.png" width="888" height="176" title="Simple Cloud Config">

Our `cloud-config.yml` is prepared for Google Cloud and our class in this `lab-1` folder.  Take a look at it now.

In order to set this configuration on our newly created BOSH director, we use
the `bosh update-cloud-config` command.

Yet we're going to want to pass in some variables like this:

```
$ bosh update-cloud-config cloud-config.yml -v internal_cidr=$MY_CIDR -v internal_gw=$MY_GW -v subnetwork_name=$MY_SUBNET
```

## Team Up

At this time, we're going to break into teams, and your goal will be to
deploy a "Hello World" BOSH release.  We recommended the
[zookeeper-release][zookeeper-release] for BOSH.

First team to get all their teammates to deploy the BOSH release, wins 10
points.  At the end of the workshop we have a prize for the team with the
most points!

When you're ready for the next lab, head on over to [lab-2][lab-2].

[//]: # (Pictures)

[mind-blown]: https://github.com/starkandwayne/operator-workshop/raw/master/images/mind-blown.gif "Mind Blown"

[//]: # (Links)

[cloud-config]: https://github.com/cloudfoundry/bosh-deployment#ops-files
[zookeeper-release]: https://github.com/cppforlife/zookeeper-release
[lab-2]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-2
