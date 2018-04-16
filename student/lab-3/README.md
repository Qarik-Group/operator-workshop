# lab-3

BOSH was created to deploy Cloud Foundry.  Our next steps will be to deploy
Cloud Foundry to containers running on our BOSH-lite director.

## How to Choose a Stemcell

When you need to deploy any release, you may need to figure out what is the right stemcell to use.  Here's a good technique to use.

### Release Notes

  * Read the Release Notes of the software you're about to deploy.

For example, on the [releases page][releases-page] for `cf-deployment` we see a section called **Release and Stemcell Updates**.  It tells us to use the `ubuntu-trusty` 3541.12 stemcell version.

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/stemcell.png" width="674" height="312" title="Stemcell">

### Target Cloud

  * Are you deploying to containers?
  * Or are you deploying to a cloud like GCP, AWS, Azure, and so on?

Go to the list of stemcells and look for the target infrastructure you're using:

http://bosh.cloudfoundry.org/stemcells/

Click the "prev..." button to get `bosh upload-stemcell` commands.  Run this to
upload our warden (container) based stemcell for BOSH-lite:

```
$ bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3541.12
```

## BOSH-lite Cloud Foundry

### Deploy CF

We are going to deploy Cloud Foundry together to our BOSH-lite director.

1. Make sure we are in our `lab-3` folder.

```
$ cd ~/operator-workshop/student/lab-3
```

2. Create a `deploy-cf.sh` file.

```
$ vi deploy-cf.sh
```

3. Insert into the `deploy-cf.sh` file, this content:

```
#!/usr/bin/env bash

set -eu

bosh -n -d cf deploy cf-deployment/cf-deployment.yml \
    --vars-store deployment-vars.yml \
    -v "system_domain=sys.$MY_EXTERNAL_IP.netip.cc" \
    -o cf-deployment/operations/bosh-lite.yml \
    -o cf-deployment/operations/use-compiled-releases.yml
```

4. We'll need the `cf-deployment` git repository.

```
$ git clone https://github.com/cloudfoundry/cf-deployment.git
```

5. Ensure our environment variables are set.

```
$ env | grep MY
```

Add them again in the SSH session if necessary.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
```

6. There is a small change to the `cloud-config` we need to fix before we can deploy.  An updated `cloud-config.yml` is included in this `lab-3` folder, it changes the initial `vm_type` from "default" to "minimal".

```
$ bosh update-cloud-config cloud-config.yml
```

7. We'll mark the `deploy-cf.sh` file as executable and run the script.

```
$ sudo chmod +x deploy-cf.sh
$ ./deploy-cf.sh
```

## CF CLI

We've installed the `cf-cli` tool on the **student-workspace** server for us to
be able to interact with our Cloud Foundry system.  First let's login.

### Authentication

1. Grab the `cf_admin_password` from the `deployment-vars.yml` store we saved all passwords to when we ran the `deploy-cf.sh` script.

```
$ bosh int deployment-vars.yml --path /cf_admin_password
```

2. The `cf login` command helps us login, because we're not using a valid SSL certificate we'll need to `--skip-ssl-validation` as we sign in.

```
$ cf login -a api.sys.$MY_EXTERNAL_IP.netip.cc --skip-ssl-validation
```

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/login-success.png" width="769" height="189" title="Login Success">

## Team Up

### Hello App

cf orgs
cf spaces
cf create-space apps -o system
cf target -o system -s apps

git clone https://github.com/cloudfoundry-community/cf-env.git
cd cf-env
cf push

## Troubleshooting Tools

cf apps

cf ssh

ps auxf

cf logs

Next is [lab-4][lab-4].

[//]: # (Links)

[releases-page]: https://github.com/cloudfoundry/cf-deployment/releases
[lab-4]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-4
