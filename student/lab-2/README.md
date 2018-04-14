# lab-2

Meanwhile, back at the ranch.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/welcome-back.gif" width="239" height="246" title="Welcome Back">

So, what happened in zookeeper when we deployed?

```
Task 5 | 15:16:05 | Updating instance zookeeper: zookeeper/b9cb899e-d37a-4e94-81e1-b43d3368bf27 (0) (canary) (00:00:51)
```

What is a [canary][canary]?

## Deployments

We're going to look at the "Hello World" deployment with some `bosh` commands.

### View

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

## Delete

We want to remove the Google Cloud deployment to make room for a BOSH-lite
deployment next.  

### Teams Exercise

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

## BOSH-lite

BOSH-lite changes the backend from being virtual machines to being containers.

Boom 1.

You can create as many containers as your host can reasonably run.

Boom 2.

Your host machine can be on the cloud.

Boom goes the dynamite!

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/dynamite.gif" width="608" height="320" title="Dynamite!">

Let's setup a BOSH-lite on Google Cloud already...

### Delete Director

1. Ensure we're still in `~/operator-workshop/student/lab-1` folder.

2. Let's create a `delete-env.sh` command.

```
$ cp create-env.sh delete-env.sh
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

3. Change to the lab-2 folder to create a BOSH-lite `create-env.sh` file.

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

6. Give your command executable permissions and run the command to create your
BOSH-lite director.

```
$ sudo chmod +x create-env.sh
$ ./create-env.sh
```

[//]: # (Pictures)



[//]: # (Links)

[canary]: https://bosh.io/docs/terminology.html#canary
[lab-3]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-3
