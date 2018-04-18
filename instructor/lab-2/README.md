# lab-2

## Timers

Build times for specific actions.

  * delete-deployment (10 mins)
  * delete-environment (5 mins)
  * bosh-lite create-env (10 mins)
  * bosh-lite zookeeper-deployment (5 mins)
  * bosh-lite delete-deployment zookeeper (seconds)

## Team Exercise Cheat Sheet

### 1. Delete Deployment

We want them to get used to searching around for commands.  Yet work looking for
the command `bosh delete-deployment`.

```
$ bosh -d zookeeper delete-deployment
```

### 2. Authorization

The users need to logout and re-authenticate to the new BOSH-lite director
before they can deploy again.

```
$ bosh logout
$ bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /director_ssl/ca)
$ export BOSH_ENVIRONMENT=bosh-director
$ bosh int ~/operator-workshop/student/lab-2/creds.yml --path /admin_password
$ bosh login
```
