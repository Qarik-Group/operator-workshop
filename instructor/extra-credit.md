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

cf marketplace -s p-mysql

cf create-service p-mysql 20mb database

cf bind-service spring-music database

cf restart spring-music
```
