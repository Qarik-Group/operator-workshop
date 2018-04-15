# lab-4

define what a service broker is in general

then what a service broker is when it comes to Cloud Foundry

we'll then deploy a MySQL BOSH release that we can use as a service broker

create the service broker in cf

show it be available in the market place

## Service Broker

```
cd ~/operator-workshop/student/lab-4
```

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

```
git clone https://github.com/cloudfoundry/cf-mysql-deployment
```

cd ~/workspace/cf-mysql-deployment
bosh -d cf-mysql deploy \
  cf-mysql-deployment.yml --vars-store mysql-creds.yml \
  -o ./operations/add-broker.yml \
  --vars-file bosh-lite/default-vars.yml \
  --var cf_mysql_external_host=p-mysql.$SYSTEM_DOMAIN \
  --var cf_mysql_host=$BOSH_ENVIRONMENT \
  --var cf_admin_password=$CF_ADMIN_PASSWORD \
  --var cf_api_url=https://api.$SYSTEM_DOMAIN \
  --var cf_skip_ssl_validation=true


bosh -e YOUR_ENV -d cf-mysql run-errand broker-registrar
