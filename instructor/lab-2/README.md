# lab-2

## timers

build times for specific actions

delete-deployment (10 mins)

delete-environment (5 mins)

bosh-lite create-env (10 minuntes)


## Team Up Cheat Sheet

The users need to logout and re-authenticate to the new BOSH-lite director.

```
$ bosh logout
$ bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int creds.yml --path /director_ssl/ca)
$ export BOSH_ENVIRONMENT=bosh-director
$ bosh int creds.yml --path /admin_password
$ bosh login
```
