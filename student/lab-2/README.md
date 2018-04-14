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

## Team Up

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
    -o bosh-deployment/external-ip-not-recommended.yml \
    -o bosh-deployment/bosh-lite.yml \
    -o bosh-deployment/bosh-lite-runc.yml \
    -o bosh-deployment/gcp/bosh-lite-vm-type.yml \
    -o ip-forwarding.yml \
    -v director_name=bosh-director \
    -v internal_cidr=$MY_CIDR \
    -v internal_gw=$MY_GW \
    -v internal_ip=$MY_INTERNAL_IP \
    --var-file gcp_credentials_json=/var/lib/gcloud/bob-the-builder.key.json \
    -v project_id=bosh-operator-class \
    -v zone=us-east1-c \
    -v tags=[internal] \
    -v network=default \
    -v subnetwork=$MY_SUBNET \
    -v external_ip=$MY_EXTERNAL_IP
```

Hit `ESC`, type `:wq` to save the contents of the file.

5. Ensure environment variables are in your SSH session, you can use the `env`
command and grep for `MY`.

```
$ env | grep MY
```

If they are not there, make sure to export them again.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
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


Once that has finished, there is a new cloud config because the new cloud are
containers instead of virtual machines.  Once again we have an example in this
`lab-2` folder.

```
$ bosh update-cloud-config cloud-config.yml -v internal_cidr=$MY_CIDR -v internal_gw=$MY_GW -v subnetwork_name=$MY_SUBNET
```

Test deploy zookeeper to the BOSH-lite.  Yet we need to upload the container
stemcell instead of the Google stemcell.

Google Stemcell looks like this:

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-google-kvm-ubuntu-trusty-go_agent
```

And the container stemcell looks like this, upload this stemcell.

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent
```

Unfortunately, if you're still in the SSH session used from before to create
the first BOSH director, you'll get an error like this:

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/x509-unknown-authority.png" width="769" height="91" title="x509 Unknown Authority">

## Team Up

What's going on with not being able to upload a stemcell?  Did we miss a step?
What step did we miss and what do we need to do?

Figure out why we can't upload the stemcell and get zookeeper deployed again.

```
$ git clone https://github.com/cppforlife/zookeeper-release.git
$ cd zookeeper-release
$ bosh -d zookeeper deploy manifests/zookeeper.yml
```

Points to the team done first.

### Result

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/zookeeper-vms-warden.png" width="700" height="172" title="Zookeeper VMS on Warden">

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/lab-2-container-results.png" width="746" height="285" title="Lab 1 VM Results">

On the next [lab-3][lab-3] we deploy Cloud Foundry to BOSH-lite!

[//]: # (Links)

[canary]: https://bosh.io/docs/terminology.html#canary
[lab-3]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-3
