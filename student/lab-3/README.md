# lab-3

How to match up the version of `cf-deployment` with what stemcell to use.

Go to [releases page][releases-page] and look for **Release and Stemcell
Updates**

< insert screenshot of what that looks like>

Make sure we have the latest version

```
$ bosh stemcells
```

Or upload it, by specifying the version.

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3541.12
```

Create a `deploy-cf.sh` file.

```
$ vi deploy-cf.sh
```

With the following variables:

```
#!/usr/bin/env bash

set -eu

bosh -d cf deploy cf-deployment/cf-deployment.yml \
    --vars-store deployment-vars.yml \
    -v "system_domain=sys.$MY_EXTERNAL_IP.netip.cc" \
    -o cf-deployment/operations/bosh-lite.yml \
    -o cf-deployment/operations/use-compiled-releases.yml
```

We'll need the `cf-deployment` git repository.

```
$ git clone https://github.com/cloudfoundry/cf-deployment.git
```

And to ensure our environment variables are set.

```
$ env | grep MY
```

Or make sure to reset them in the SSH session.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
```

```
$ sudo chmod +x deploy-cf.sh
$ ./deploy-cf
```

this takes about 20 minutes.

Grab the `cf_admin_password` from the `deployment-vars.yml` store.

```
$ bosh int deployment-vars.yml --path /cf_admin_password
```

And log into `cf`:

```
$ cf login -a api.sys.$MY_EXTERNAL_IP.netip.cc --skip-ssl-validation
```

[//]: # (Links)

[releases-page]: https://github.com/cloudfoundry/cf-deployment/releases
