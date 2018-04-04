# operator-workshop

lab material for cf summit operator workshop

## Initial Setup

1. Ensure you have [access to the infrastructure][lab-0].

2. After signing in to your Google Account, start a [Google Cloud Shell][cloud-shell].

3. Clone this repo in your home folder and update submodules:

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
$ cd operator-workshop
$ git submodule update --init --recursive
```

This will copy all our labs down to your Google Cloud Shell that we'll use today.

## Tools

At any time, you may need to install or re-install `bosh-cli`, `terraform`, `bbl` in the `/usr/local/bin`.

From the root of the `~/operator-workshop` folder, you can run this to re-install the tools we'll use each each lab.

```
$ sudo bin/install-tools
```

[lab-0]: https://github.com/starkandwayne/operator-workshop/tree/master/lab-0
[cloud-shell]: https://cloud.google.com/shell/
