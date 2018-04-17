# troubleshooting

## Setup BOSH SSH

1. Ensure you have your environment variables [set from lab-1][set-env].

```
$ . ~/set-env
```

2. Create a [jumpbox][jumpbox] key.

```
$ bosh int ~/operator-workshop/student/lab-2/creds.yml --path /jumpbox_ssh/private_key > ~/jumpbox.key
```

3. Correct the permissions of the key file.

```
$ chmod 600 ~/jumpbox.key
```

4. Start a [SOCKS5 tunnel][socks5].

```
ssh -4 -D 12345 -fNC jumpbox@$MY_EXTERNAL_IP -i ~/jumpbox.key
```

5. Export a `BOSH_ALL_PROXY` environment variable to the socks5 proxy.

```
$ export BOSH_ALL_PROXY=socks5://localhost:12345
```

6. Reconnect to the BOSH director with your alias.  For this operator workshop
the bosh-lite is in `~/operator-workshop/student/lab-2`

```
bosh alias-env bosh-director -e $MY_EXTERNAL_IP --ca-cert <(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /director_ssl/ca)
```

And the `BOSH_CLIENT` and `BOSH_CLIENT_SECRET` can be set as environment
variables like so:

```
$ export BOSH_CLIENT=admin
$ export BOSH_CLIENT_SECRET=$(bosh int ~/operator-workshop/student/lab-2/creds.yml --path /admin_password)
```

7. Login.

```
$ bosh login
```

This is all so that you can SSH to BOSH-lite over a SOCKS5 proxy using the
jumpbox key that was generated when we deployed.

[//]: # (Links)

[jumpbox]: https://bosh.io/docs/jumpbox
[socks5]: https://bosh.io/docs/cli-tunnel.html
[set-env]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1#set-env
