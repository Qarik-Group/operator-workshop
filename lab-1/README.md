# lab 1

Do the "getting started GCP" stuff

gcloud iam service-accounts create <service account name>

gcloud iam service-accounts keys create --iam-account='<service account name>@<project id>.iam.gserviceaccount.com' <service account name>.key.json

gcloud projects add-iam-policy-binding <project id> --member='serviceAccount:<service account name>@<project id>.iam.gserviceaccount.com' --role='roles/editor'

[deploying bosh-lite on GCP][bosh-lite]

cd lab-1
export BBL_GCP_SERVICE_ACCOUNT_KEY=~/operator-workshop/<service account name>.key.json
bbl plan --name some-env --iaas gcp --gcp-region us-west1
git submodule update --init --recursive
cp -r ../bosh-bootloader/plan-patches/bosh-lite-gcp/* .


[bosh-lite]: https://github.com/cloudfoundry/bosh-bootloader/blob/master/docs/advanced-configuration.md#deploying-bosh-lite-on-gcp
