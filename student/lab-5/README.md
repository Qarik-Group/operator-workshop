# lab-5

The road goes on for you from here by exploring on your own with the following
self-led resources.

## Choose a Cloud

In order to learn more BOSH, you need a "lab".  This can be accomplished a couple
of ways.  Either setup one on a public cloud.  Or on your own server or laptop
you own.

### Public Clouds

Trial accounts are a great way to do rapid testing and a Proof of Concept (POC).

* [Google Cloud Platform (GCP)][gcp]
* [Amazon Web Services Elastic Compute Cloud (AWS EC2)][aws]
* [Microsoft® Azure][azure]

### Bare-Metal

Or if you have your own hardware, like a laptop or a server you can use
VirtualBox to simulate a cloud for you.

* Use the [how to install BOSH Lite with VirtualBox][bosh-lite] instructions.

NOTE:  Keep in mind that long term though, a BOSH-lite is best for learning and
perhaps for developing locally a release.  When setting up a CI/CD pipeline it's
better to have the same cloud in each environment.

## Build a Release

Once you have a lab to work with, these two guides will step you through how a
BOSH release is built.  This is important because once you know how a release
is made, you can become literate in evaluating "off the shelf" releases available
in the Open Source Software (OSS) community.  For example, on https://bosh.io/releases.

1. Learn how to build a release with the [Learn BOSH][mariash] tutorial.
2. Also you can follow the **bosh.io** guide for how to [create a release][create-release].

[//]: # (Links)

[gcp]:            https://cloud.google.com/free/
[aws]:            https://aws.amazon.com/free/
[azure]:          https://azure.microsoft.com/en-us/free/
[mariash]:        https://mariash.github.io/learn-bosh/
[create-release]: https://bosh.io/docs/create-release/
[bosh-lite]:      https://bosh.io/docs/bosh-lite/
