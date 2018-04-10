# lab-0 preparation

These are the steps to get the the labs ready as an instructor.

## Student Workspace

Why a VM on the cloud as the workspace?

It allows users to have any local platform (Windows, Mac, Linux) and not need
anything more than internet access, a modern OS and web browser.

The hope is this provides a quick, level playing field for all students in
attendance.

### Networking

For this lab, we're using the default `10.142.0.0/20` in the **us-east1** region.

### Firewall

To ensure firewalls don't become an issue.  Disable them on the internal network.

Create rules that allow ingress/egress for any traffic on `10.142.0.0/20` on any port.

### Workspace

This simplifies us not having to setup individual student machines, and keeps
it straight for the student too.

1. Under Compute Engine, **Create an Instance**.

  * name: **student-workspace**
  * zone: us-east-1b (or near you)
  * machine type: **n1-highmem-64**
  * boot disk: **Ubuntu 14.04 LTS**
  * identity and API access: **Allow full access to all Cloud APIs**

2. Under the Management, disks, networking, SSH keys, switch to the **Networking** tab.

3. Change the Network interface tab to the **student-network-1**.

4. **Create** the VM.

### Software

SSH onto the **student-workspace** vm when it's ready.

In order to be ready to perform the first lab we need to clone this very repo.  
But we need `git.`

```
$ sudo apt-get install -y git
```

Then we can clone the repo and install the rest.

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
cd operator-workshop/
sudo bin/install-tools
```

Ready to rock, let's get [lab-1][lab-1] specific requirements setup now.

[lab-1]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1
