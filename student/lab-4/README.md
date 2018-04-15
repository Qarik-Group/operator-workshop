# lab-4

define what a service broker is in general

then what a service broker is when it comes to Cloud Foundry

we'll then deploy a MySQL BOSH release that we can use as a service broker

create the service broker in cf

show it be available in the market place

## Service Broker

Ensure directory.

```
cd ~/operator-workshop/student/lab-4
```

Make sure we're logged into BOSH director and have correct environment variables.

```
export MY_CIDR=10.42.1.0/24
export MY_GW=10.42.1.1
export MY_INTERNAL_IP=10.42.1.10
export MY_EXTERNAL_IP=35.196.19.152
export MY_SUBNET=student-1

bosh logout
bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /director_ssl/ca)
export BOSH_ENVIRONMENT=bosh-director
bosh int ~/operator-workshop/student/lab-2/creds.yml --path /admin_password
bosh login
```

Clone the `cf-mysql-deployment` repo.

```
git clone https://github.com/cloudfoundry/cf-mysql-deployment
```

Create new environment variables used in this deploy.

```
export BOSH_ENVIRONMENT=bosh-director
export SYSTEM_DOMAIN=sys.$MY_EXTERNAL_IP.netip.cc
export CF_ADMIN_PASSWORD=$(bosh int ~/operator-workshop/student/lab-3/deployment-vars.yml --path /cf_admin_password)
```

Upload the BOSH release BLOB.

```
$ bosh upload-release https://bosh.io/d/github.com/cloudfoundry/cf-mysql-release --sha1 401cd27775423ee6b3a6e53e89010261c17e0c5c
```

Create the cf-mysql.sh deploy script.

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
  --vars-file cf-mysql-deployment/bosh-lite/default-vars.yml \
  -v cf_mysql_external_host=p-mysql.$SYSTEM_DOMAIN \
  -v cf_mysql_host=$BOSH_ENVIRONMENT \
  -v cf_admin_password=$CF_ADMIN_PASSWORD \
  -v cf_api_url=https://api.$SYSTEM_DOMAIN \
  -v cf_skip_ssl_validation=true
```

When deploy is complete run the broker registrar.

```
bosh -d cf-mysql run-errand broker-registrar
```
