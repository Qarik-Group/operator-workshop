# lab-2

Meanwhile, back at the ranch.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/welcome-back.gif" width="239" height="246" title="Welcome Back">

So, what happened in zookeeper when we deployed?

```
Task 5 | 15:16:05 | Updating instance zookeeper: zookeeper/b9cb899e-d37a-4e94-81e1-b43d3368bf27 (0) (canary) (00:00:51)
```

What is a [canary][canary]?  What would a canary protect against?

## Deployments

We're going to look at the "Hello World" deployment with some `bosh` commands.

### View Deployment

Here's how we look at the zookeeper deployment.  First, if we've forgotten the
name of the deployment we can get a list of all deployments like this.

```
$ bosh deployments
```

Then with the name of the deployment we can see what virtual machines are
running in our deployment:

```
$ bosh -d zookeeper vms
```

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/zookeeper-vms-google.png" width="700" height="172" title="Zookeeper VMS on Google">

Notice the IP addresses are all in our internal student subnet of `10.42.1.0/24.`

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/lab-1-vm-results.png" width="749" height="365" title="Lab 1 VM Results">

And it created full virtual machines in Google Cloud.

Next we are going to delete this deployment and create a container-based
deployment on what's called a BOSH-lite system.

## Team Exercise

We want to remove the Google Cloud deployment to make room for a BOSH-lite
deployment next.  

### Delete Deployment

To delete our "Hello World" deployment, what command will we use? Let's talk
about a technique we can use to find commands. Run `bosh` with no sub-command
to see a list of available sub-commands.

```
$ bosh
```

Read through the list of sub-commands to find something that will remove or
delete a deployment.

Once you have a command to try then run this command to get additional help:

```
$ bosh <command> --help
```

Another thing that's great with each command, is that a direct link to docs is
at the top: https://bosh.io/docs/cli-v2

First team again, with their deployments deleted, adds ten more points.

## BOSH-lite

* BOSH-lite changes the backend from being virtual machines to being containers. Boom 1.
* You can create as many containers as your host can reasonably run. Boom 2.
* Your host machine can be on the cloud. Boom goes the dynamite!

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/dynamite.gif" width="608" height="320" title="Dynamite!">

Let's setup a BOSH-lite on Google Cloud already...

### Delete Director

NOTE: Have you deleted your "Hello World" deployment first?  Please make sure
you've done this first before deleting the director.  Our helpful instructors
are standing by for assistance.

1. Ensure we're still in `~/operator-workshop/student/lab-1` folder.

2. Let's create a `delete-env.sh` command.

```
$ cp create-env.sh delete-env.sh
$ vi delete-env.sh
```

Edit the file with `vi` and change the command from `create-env` to
`delete-env`.  This will let the BOSH command-line clean up the files
from the cloud and local file system.

```
$ ./delete-env.sh
```

NOTE: Are any files left behind?  Why?

Now that we've deleted the deployment and the director we're ready to create
a BOSH-lite.

### Create BOSH-lite

3. Change to the `lab-2` folder to create a BOSH-lite `create-env.sh` file.

```
$ cd ~/operator-workshop/student/lab-2
```

4. Using `vi` let's create another `create-env.sh` file, this time with
additional operator files and variables that will help us setup a BOSH-lite
environment on Google Cloud.

```
$ vi create-env.sh
```

In `vi` press `i` to begin "insert mode" and then, copy and paste the following:

```
#!/usr/bin/env bash

set -eu

bosh create-env bosh-deployment/bosh.yml \
    --state=state.json \
    --vars-store=creds.yml \
    -o bosh-deployment/gcp/cpi.yml \
    -o bosh-deployment/uaa.yml \
    -o bosh-deployment/credhub.yml \
    -o bosh-deployment/bosh-lite.yml \
    -o bosh-deployment/bosh-lite-runc.yml \
    -o bosh-deployment/gcp/bosh-lite-vm-type.yml \
    -o bosh-deployment/jumpbox-user.yml \
    -o ip-forwarding.yml \
    -v director_name=bosh-director \
    -v internal_cidr=$MY_CIDR \
    -v internal_gw=$MY_GW \
    -v internal_ip=$MY_INTERNAL_IP \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-training-2019 \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=$MY_SUBNET \
```

Hit `ESC`, type `:wq` to save the contents of the file.

5. Ensure environment variables are in your SSH session, you can use the `env`
command and grep for `MY`.

```
$ env | grep MY
```

If they are not there, make sure to export them again with the
[set-env][set-env] helper we created at the beginning of `lab-1`.

```
$ . ~/set-env
```

6. Since we are in a new lab folder we'll quickly clone down the
`bosh-deployment` git repo again.

```
$ git clone https://github.com/cloudfoundry/bosh-deployment
```

7. Give your command executable permissions and run the command to create your
BOSH-lite director.

```
$ sudo chmod +x create-env.sh
$ ./create-env.sh
```

## Deploy Zookeeper to BOSH-lite

Our BOSH-lite is deployed.  It's now our BOSH-director and host for containers
all rolled into one server.  We want to deploy our "Hello World" zookeeper
BOSH release again.  Here's what we'll do:

First update the `cloud-config`:

```
$ bosh update-cloud-config cloud-config.yml
```

And we'll need a container stemcell instead of the Google Cloud stemcell.

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent
```

Finally, we can deploy zookeeper:

```
$ git clone https://github.com/cppforlife/zookeeper-release.git
$ bosh -d zookeeper deploy zookeeper-release/manifests/zookeeper.yml
```

### Error

Yet before we can get any of the above commands to work, we get an error like
below: `x509 Unknown Authority`.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/x509-unknown-authority.png" width="769" height="91" title="x509 Unknown Authority">

## Team Exercise

It's time to Team Exercise again! Figure out what we need to do so we can deploy the "Hello World" release.

10 points to the team done first.

## BOSH-lite Review

Again, all of this happens on one, generally larger host computer.  For our
example we're on a **n1-standard-8 (8 vCPUs, 30 GB memory)** for Google Cloud.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/lab-2-container-results.png" width="746" height="285" title="Lab 1 VM Results">

Since we have completed our deployment of zookeeper again, we can use the
`bosh vms` command to review what instances are running in our deployment.

```
$ bosh -d zookeeper vms
```

The IP addresses are different from our cloud IP addresses of `10.42.X.0/24`
before and are now on a `10.244.0.0/24` as configured in our
`lab-2/cloud-config.yml` file.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/zookeeper-vms-warden.png" width="700" height="172" title="Zookeeper VMS on Warden">

To get ready to install Cloud Foundry on our BOSH-lite, let's make room for it
by deleting our "Hello World" deploy again.

```
$ bosh -d zookeeper delete-deployment
$ bosh deployments
```

The list in `bosh deployments` should be 0, well done.

![well-done][well-done]

On to our the next [lab-3][lab-3].  Where we will deploy Cloud Foundry to
BOSH-lite!

[//]: # (Pictures)

[well-done]: https://github.com/starkandwayne/operator-workshop/raw/master/images/well-done.gif "Well Done"

[//]: # (Links)

[canary]: https://bosh.io/docs/terminology.html#canary
[lab-3]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-3
[set-env]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1#set-env
