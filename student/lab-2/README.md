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
