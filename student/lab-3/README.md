# lab-3

In lab-2, we created a BOSH-lite.  It's running it's own embedded BOSH director.
We can use this BOSH-lite to deploy software.  After deploying and removing the
"Hello World" zookeeper deploy, we're ready now to deploy Cloud Foundry to our
BOSH-lite server.

## Cloud Foundry

This course will not focus on how Cloud Foundry operates, for more about that we
recommend the rest of this conference, future training focused on Cloud Foundry,
or [Introduction to Cloud Foundry and Cloud Native Software Architecture][mooc]
self-led course.

### Deploy

Follow these steps to setup a Cloud Foundry deployment script.

1. Make sure we are in our `lab-3` folder on our **student-workspace** server.

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

6. As you may have noticed now, it is common to get your `cloud-config` ready
before you deploy.  Each deployment repo comes with examples or reference
material before you prepare you deployment.  We're using the
[bosh-lite cloud-config][cf-cc] for our deploy.


```
$ bosh update-cloud-config cloud-config.yml
```

7. We'll mark the `deploy-cf.sh` file as executable and run the script.

```
$ sudo chmod +x deploy-cf.sh
$ ./deploy-cf.sh
```

This will take about 20 minutes to complete.

## CF-CLI

We've installed the `cf-cli` tool on the **student-workspace** server for us to
be able to interact with our Cloud Foundry system.  First let's login.

### Authentication

1. Grab the `cf_admin_password` from the `deployment-vars.yml` store we saved
all passwords to when we ran the `deploy-cf.sh` script.

```
$ bosh int deployment-vars.yml --path /cf_admin_password
```

2. The `cf login` command helps us login, because we're not using a valid SSL
certificate we'll need to `--skip-ssl-validation` as we sign in.

```
$ cf login -a api.sys.$MY_EXTERNAL_IP.netip.cc --skip-ssl-validation
```

<img src="https://github.com/starkandwayne/operator-workshop/raw/master/images/login-success.png" width="769" height="189" title="Login Success">

### Team Exercise

Deploy a Hello World App to Cloud Foundry.

```
$ git clone https://github.com/cloudfoundry-community/cf-env.git
```

First team done gets another 10 more smack-a-rack-a-points to the big board!

### App Troubleshooting Commands

Run `cf apps` to see what apps are running.

```
$ cf apps
Getting apps in org system / space apps as admin...
OK

name     requested state   instances   memory   disk   urls
cf-env   started           1/1         256M     1G     cf-env.sys.35.196.19.152.netip.cc
```

* Connect to your app instance with `cf ssh`:

```
$ cf ssh cf-env
```

* Check your logs with

```
$ cf logs cf-env
```
* See what environment variables set or services bound with `cf env`:

```
$ cf env cf-env
```

Coming up next is [lab-4][lab-4], where we add a MySQL service broker to our
Cloud Foundry platform.

[//]: # (Links)

[releases-page]: https://github.com/cloudfoundry/cf-deployment/releases
[lab-4]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-4
[set-env]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1#set-env
[cf-cc]: https://github.com/cloudfoundry/cf-deployment/blob/master/iaas-support/bosh-lite/cloud-config.yml
[mooc]: https://www.edx.org/course/introduction-cloud-foundry-cloud-native-linuxfoundationx-lfs132x
