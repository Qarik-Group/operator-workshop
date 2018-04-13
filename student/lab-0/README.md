# lab-0

## Setup Google Cloud

1. Notify [the instructor][instructor_email] with your Google Account email address.  This is used to provide access to the lab resources.

2. Check your email that notifies you of access, then log into https://console.cloud.google.com.

## Confirm Project Access

![bosh-operator-class][bosh-operator-class]

1. You can confirm you have access when you have logged into Google Cloud and can see the [Project info][identifying_projects] says **Project Name** of "bosh-operator-class".

![ssh-button][ssh-button]

2. Click on the [SSH button][ssh-button] to connect to the **student-workspace** server.

3. Clone this repo:

```
$ git clone https://github.com/starkandwayne/operator-workshop.git
```

This copies all our labs down to your folder on the **student-workspace** server.  We use a VM running on the target infrastructure because this gives us a common denominator for each of us who might be using different computer platforms to use this workshop (Windows, Mac, Linux).

## Ready

You are now ready to run [lab 1][lab-1].  There will be some discussion first and then we'll jump right in.

[//]: # (Pictures)

[bosh-operator-class]: https://github.com/starkandwayne/operator-workshop/raw/master/images/bosh-operator-class.png "BOSH Operator Class"
[ssh-button]: https://github.com/starkandwayne/operator-workshop/raw/master/images/ssh-button.png "SSH Button"

[//]: # (Links)

[instructor_email]: <mailto:tbird@starkandwayne.com>
[identifying_projects]: https://cloud.google.com/resource-manager/docs/creating-managing-projects#identifying_projects
[ssh-button]: https://cloud.google.com/compute/docs/instances/connecting-to-instance
[lab-1]: https://github.com/starkandwayne/operator-workshop/tree/master/student/lab-1
