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

### Errands

```
$ bosh -d cf-mysql errands
```

```
$ bosh -d cf-mysql run-errand broker-registrar
```

```
$ bosh -d cf-mysql run-errand smoke-tests
```

[//]: # (Links)

[set-env]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1#set-env
