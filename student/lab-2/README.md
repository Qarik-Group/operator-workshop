# lab-2

What happened in zookeeper?

```
Task 5 | 15:16:05 | Updating instance zookeeper: zookeeper/b9cb899e-d37a-4e94-81e1-b43d3368bf27 (0) (canary) (00:00:51)
```

What is a [canary][canary]?

## Deployments

### View

Let's look at the zookeeper deployment we've got running.

```
$ bosh deployments
```

We can see what virtual machines are running from this deployment like this:

```
$ bosh -d zookeeper vms
```

### Remove

We are going to remove our hello world deployment.  Yet what command will we use?
Let's talk about a technique we can use to find commands. Run `bosh` with no
commands to see a list.

```
$ bosh
```

Read through the list of commands to find something that will remove or delete a deployment.

Once you have a command then run this command to get additional help for that command:

```
$ bosh <command> --help
```

Another thing that's great with each command, is that a direct link to
the https://bosh.io/docs/cli-v2 docs at the top.

## BOSH-lite

### Delete Director

Still in `~/operator-workshop/student/lab-1` let's create a `delete-env.sh` command.

```
$ cp create-env.sh delete-env.sh
```

Edit the file with `vi` and change the command from `create-env` to
`delete-env`.  This will let the BOSH command-line clean up the files
from the cloud and local file system.

```
$ ./delete-env.sh
```

It's time to switch directories to lab-2.

```
$ cd ~/operator-workshop/student/lab-2
```

### Create BOSH-lite

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

Ensure environment variables are there.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
```

chmod and run

```
$ sudo chmod +x create-env.sh
$ ./create-env.sh
```

[//]: # (Links)

[canary]: https://bosh.io/docs/terminology.html#canary
