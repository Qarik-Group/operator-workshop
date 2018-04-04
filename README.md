# operator-workshop

This repo contains code and lab material for the Cloud Foundry Summit 2018 BOSH Operator Workshop class.

## Initial Setup

1. Ensure you have [access to the infrastructure][lab-0].

2. Next, sign in to your Google Account, and start a [Google Cloud Shell][cloud-shell].

3. Clone this repo in your home folder and update submodules:

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
$ cd operator-workshop
$ git submodule update --init --recursive
```

This copies all our labs down to your **Google Cloud Shell**.

## Tools

At any time, you may need to install or re-install `bosh-cli`, `terraform`, `bbl` in the `/usr/local/bin`.

From the root of the `~/operator-workshop` folder, you can run this to re-install the tools we'll use.

```
$ sudo bin/install-tools
```

[lab-0]: https://github.com/starkandwayne/operator-workshop/tree/master/lab-0
[cloud-shell]: https://cloud.google.com/shell/
