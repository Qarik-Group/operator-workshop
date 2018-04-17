## Service Broker

Now MySQL is available in Cloud Foundry's marketplace.

```
$ cf marketplace
```

## Team Up

Using `cf create-service` how do we create a service?

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

clone spring music

```
git clone https://github.com/cloudfoundry-samples/spring-music.git
cd spring-music
./gradlew clean assemble
cf push
```

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
```
