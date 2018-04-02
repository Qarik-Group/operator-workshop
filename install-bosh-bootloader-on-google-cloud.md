# Install BBL on Google Cloud

For when you have no shell but Google Cloud Shell and want to use [BOSH Bootloader][bbl] to get going.

Requirements for BBL out of the box Cloud Shell has:

  - [x] bosh-cli
  - [ ] bosh create-env dependencies
  - [ ] terraform
  - [x] ruby

Which means we need to install a few things.

`/usr/local/bin` is already in the PATH for Cloud Shell

[bbl]: https://github.com/cloudfoundry/bosh-bootloader
