# operator-workshop

This repo contains code and lab material for the Cloud Foundry Summit 2018 BOSH Operator Workshop class.

## Setup

1. Ensure you have [access to the infrastructure][lab-0].

2. Next, sign in to your Google Account, and click on the [SSH button][ssh-button] to connect to the **student-workspace** server.

3. Clone this repo in your home folder and update submodules:

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
$ cd operator-workshop
$ git submodule update --init --recursive
```

This copies all our labs down to the **student-workspace** server.  We use a VM running on the target infrastructure because this gives us a common denominator for each of us who might be using different computer platforms to use this workshop (Windows, Mac, Linux).

## Ready

You are now ready to run [lab 1][lab-1].  There will be some discussion first and then we'll jump right in.

[lab-0]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-0
[ssh-button]: https://cloud.google.com/compute/docs/instances/connecting-to-instance
[lab-1]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1
