# lab-4

  * define what a service broker is in general
  * then what a service broker is when it comes to Cloud Foundry
  * we'll then deploy a MySQL BOSH release that we can use as a service broker
  * create the service broker in cf
  * show it be available in the market place

## cf-mysql-deployment

### Deploy

1. Ensure you're in the `~/operator-workshop/student/lab-4` directory.

```
cd ~/operator-workshop/student/lab-4
```

2. Ensure environment variables are in your SSH session, you can use the `env`
command and grep for `MY`.

```
$ env | grep MY
```

If they are not there, make sure to export them again with the
[set-env][set-env] helper we created at the beginning of `lab-1`.

```
$ . ~/set-env
```

3. Are you logged into the BOSH director?

```
bosh logout
bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /director_ssl/ca)
bosh int ~/operator-workshop/student/lab-2/creds.yml --path /admin_password
bosh login
```

4. Clone the `cf-mysql-deployment` repo.

```
git clone https://github.com/cloudfoundry/cf-mysql-deployment
```

5. Create new environment variables used in this deploy.

```
export BOSH_ENVIRONMENT=bosh-director
export SYSTEM_DOMAIN=sys.$MY_EXTERNAL_IP.netip.cc
export CF_ADMIN_PASSWORD=$(bosh int ~/operator-workshop/student/lab-3/deployment-vars.yml --path /cf_admin_password)
```

6. Upload the BOSH release BLOB.

```
$ bosh upload-release https://bosh.io/d/github.com/cloudfoundry/cf-mysql-release --sha1 401cd27775423ee6b3a6e53e89010261c17e0c5c
```

7. Create the `cf-mysql.sh` deploy script.

```
$ vi cf-mysql.sh
```

Insert:

```
#!/usr/bin/env bash

set -eu

bosh -n -d cf-mysql deploy cf-mysql-deployment/cf-mysql-deployment.yml \
  --vars-store mysql-creds.yml \
  -o cf-mysql-deployment/operations/add-broker.yml \
  -o cf-mysql-deployment/operations/bosh-lite.yml \
  -o cf-mysql-deployment/operations/latest-versions.yml \
  -o cf-mysql-deployment/operations/register-proxy-route.yml \
  --vars-file cf-mysql-deployment/bosh-lite/default-vars.yml \
  -v cf_mysql_external_host=p-mysql.$SYSTEM_DOMAIN \
  -v cf_mysql_host=$BOSH_ENVIRONMENT \
  -v cf_admin_password=$CF_ADMIN_PASSWORD \
  -v cf_api_url=https://api.$SYSTEM_DOMAIN \
  -v cf_skip_ssl_validation=true
```

8. Update the cloud-config.

```
$ bosh update-cloud-config cloud-config.yml
```

9. Set the security group.

```
cf create-security-group p-mysql sg.json
cf bind-running-security-group p-mysql
```

10. Run deploy.

```
$ sudo chmod +x cf-mysql.sh
$ ./cf-mysql.sh
```

When deploy is complete run the broker registrar.

### Post Deploy

how to see list of available errands is `bosh errand` with the name of the deployment.

```
$ bosh errands -d cf-mysql
```

Now MySQL is available in Cloud Foundry's marketplace.

```
$ cf marketplace
```

## Team Up


Using `cf create-service` how do we create a service?


```
bosh -d cf-mysql run-errand broker-registrar
```

## Spring Music

Deploy spring music app

it uses "in-memory" database first.

Then use the MySQL we've deployed.

Bind the service to the app.

Restage the app.

cf marketplace -s p-mysql


in the right folder

```
cd operator-workshop/student/lab-4
```

login helper

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1
bosh logout
bosh alias-env bosh-director -e $MY_INTERNAL_IP --ca-cert <(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /director_ssl/ca)
export BOSH_ENVIRONMENT=bosh-director
bosh int ~/operator-workshop/student/lab-2/creds.yml --path /admin_password
bosh login
```

clone spring music

```
git clone https://github.com/cloudfoundry-samples/spring-music.git
cd spring-music
gradle clean assemble
cf push
```

bosh upload-release https://bosh.io/d/github.com/cloudfoundry-incubator/cf-routing-release?v=0.145.0

```
cf marketplace

cf services

cf marketplace -s p-mysql

cf create-service p-mysql 20mb database

cf apps

cf services

cf create-security-group cf-mysql rule.json
cf bind-running-security-group cf-mysql

cf bind-service spring-music database

cf restart spring-music

cf logs spring-music --recent
cf bind

cf unbind-service
cf services
bosh -d cf-mysql vms

cf service
cf service spring-music-db
bosh int ~/operator-workshop/student/lab-3/deployment-vars.yml --path /cf_admin_password
cf app
cf apps
cf app spring-music

cf bind-service
cf services
cf services -h
cf service -h
cf service spring-music-db

cf bind-service -h
cf apps
cf unbind-service -h
cf unbind-service spring-music spring-music-db
cf bind-service spring-music spring-music-db
cf restart
cf restart spring-music
cf logs spring-music --recent
cf env spring-music
cf unbind-service spring-music spring-music-db
cf restart spring-music
cf env spring-music


ideas

build spring-music locally

uninstall gradle on server?

clear the gradle cache?

rm -rf ~/.gradle
```

[//]: # (Links)

[set-env]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1#set-env
