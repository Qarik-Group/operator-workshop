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

We are going to remove our hello world deployment.  Yet what command will we use?  Let's talk about a technique we can use to find commands.
Run `bosh` with no commands to see a list.

```
$ bosh
Please specify one command of: add-blob, alias-env, attach-disk, blobs, cancel-task, clean-up, cloud-check, cloud-config, c
onfig, configs, cpi-config, create-env, create-release, delete-config, delete-deployment, delete-disk, delete-env, delete-r
elease, delete-snapshot, delete-snapshots, delete-stemcell, delete-vm, deploy, deployment, deployments, diff-config, disks,
 environment, environments, errands, event, events, export-release, finalize-release, generate-job, generate-package, help,
 ignore, init-release, inspect-release, instances, interpolate, locks, log-in, log-out, logs, manifest, orphan-disk, recrea
te, releases, remove-blob, repack-stemcell, reset-release, restart, run-errand, runtime-config, scp, snapshots, ssh, start,
 stemcells, stop, sync-blobs, take-snapshot, task, tasks, unignore, update-cloud-config, update-config, update-cpi-config,
update-resurrection, update-runtime-config, upload-blobs, upload-release, upload-stemcell, variables, vendor-package or vms
Exit code 1
```

Read through the list of commands to find something that will remove or delete a deployment.

Once you have a command then run this command to get additional help for that command:

```
$ bosh <command> --help
```

Another thing that's great with each command, is that a direct link to
the https://bosh.io/docs/cli-v2 docs at the top.

### Change

[//]: # (Links)

[canary]: https://bosh.io/docs/terminology.html#canary
