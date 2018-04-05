# Setup BOSH Lite and Cloud Foundry with BBL on GCP

# CLI Versions
```
$ cf -v
cf version 6.35.2+88a03e995.2018-03-15
```

```
$ bbl -v
6.4.8 (darwin/amd64)
```

```
# bosh -v
# 3.0.1-712bfd7-2018-03-13T23:26:42Z
```

```
# gcloud version
#     Google Cloud SDK 196.0.0
#     bq 2.0.31
#     core 2018.03.30
#     gsutil 4.30
```

# make some directories to segment out our work
mkdir -p bbl-gcp-bosh-lite/bbl
cd bbl-gcp-bosh-lite

# clone these two repos
git clone https://github.com/cloudfoundry/cf-deployment.git
git clone https://github.com/cloudfoundry/bosh-bootloader.git

# optional step if you want to use the exact same commits I used
cd cf-deployment
get checkout fe2817c

cd ../bosh-bootloader
git checkout fa4d0f45
cd ..

cd bbl

export GCP_SERVICE_ACCOUNT=boshlite-gcpcf #names you decide
export GCP_PROJECT=boshlite-cf # names you decide
export BBL_IAAS=gcp # don't change this
export BBL_GCP_REGION=us-west1 # pick a region
export BBL_ENV_NAME=boshlite # you decide
export BBL_GCP_SERVICE_ACCOUNT_KEY=$GCP_SERVICE_ACCOUNT.key.json # don't changes this

# create a GCP project
gcloud projects create $GCP_PROJECT

# create a service account and assign our project to it
gcloud iam service-accounts create $GCP_SERVICE_ACCOUNT --project $GCP_PROJECT
# create service account keys so BBL can talk to GCP
gcloud iam service-accounts keys create --iam-account="$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" $BBL_GCP_SERVICE_ACCOUNT_KEY

# add all the roles we need to our GCP service account
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/editor'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.instanceAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.networkAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.securityAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.storageAdmin'

# enable billing and link it to the project on your account. to do this you will need to go into the console. The easiest way to find it is search billing.

# enable the compute api
gcloud services enable compute.googleapis.com --project=$GCP_PROJECT

bbl plan

cp -r ../bosh-bootloader/plan-patches/bosh-lite-gcp/* .
bbl up

# setup our environment variables for BOSH
eval "$(bbl print-env)"

# depending on the version of cf-deployment you are using you may need to get a different stemcell version.
bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3541.10

bosh -n -d cf deploy -v 'system_domain=sys.<your-domain>' -o ../cf-deployment/operations/bosh-lite.yml -o ../cf-deployment/operations/use-compiled-releases.yml --vars-store deployment-vars.yml ../cf-deployment/cf-deployment.yml

gcloud compute instances list --project $GCP_PROJECT

# look for the line with the internal ip of 10.0.0.6 and copy the external ip

# go to your DNS provider and add the following entries
```
*.sys.<your-domain>.com - A to <external ip>
```

# find cf_admin password in ./deployment-vars.yml
cf login -a api.sys.<yourdomain.com> # use the username admin and the password you found

# run cf commands! and profit!

# to tear everything down you can use - bbl destroy
