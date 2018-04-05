# Setup BOSH Lite and Cloud Foundry with BBL on GCP

## CLI Versions
Your versions most likely don't have to match, but just incase you wanted to know what was used

```
$ gcloud version
Google Cloud SDK 196.0.0
bq 2.0.31
core 2018.03.30
gsutil 4.30
```

```
$ bbl -v
6.4.8 (darwin/amd64)
```

```
$ bosh -v
3.0.1-712bfd7-2018-03-13T23:26:42Z
```

** If you are installing Cloud Foundry you will need the cf cli to interact with it **
```
$ cf -v
cf version 6.35.2+88a03e995.2018-03-15
```

## Make some directories to segment out our work
```
mkdir -p bbl-gcp-bosh-lite/bbl
cd bbl-gcp-bosh-lite
```

** clone these two repos **
```
git clone https://github.com/cloudfoundry/cf-deployment.git
git clone https://github.com/cloudfoundry/bosh-bootloader.git
```

** optional step if you want to use the exact same commits I used **
```
cd cf-deployment
git checkout fe2817c

cd ../bosh-bootloader
git checkout fa4d0f45
cd ..
```

** Go into your BBL directory **
```
cd bbl
```

## Environment variables
** After this secion I am going to assume you have these set **

** You can decide on the values for these **
```
export GCP_SERVICE_ACCOUNT=<names you decide>
export GCP_PROJECT=<names you decide>
export BBL_ENV_NAME=<names you decide>
```

** Pick a region within GCP I have it us-west1 but feel free to pick a different one**
```
export BBL_GCP_REGION=us-west1
```

** Leave these alone  **
```
export BBL_IAAS=gcp
export BBL_GCP_SERVICE_ACCOUNT_KEY=$GCP_SERVICE_ACCOUNT.key.json
```

## GCP Setup
### Create a GCP project
```
gcloud projects create $GCP_PROJECT
```

### Create a service account and assign our project to it
```
gcloud iam service-accounts create $GCP_SERVICE_ACCOUNT --project $GCP_PROJECT
```
### Create the service accounts keys so BBL can talk to GCP
```
gcloud iam service-accounts keys create --iam-account="$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" $BBL_GCP_SERVICE_ACCOUNT_KEY
```
### Add all the roles we need to our GCP service account
```
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/editor'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.instanceAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.networkAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.securityAdmin'
gcloud projects add-iam-policy-binding $GCP_PROJECT --member="serviceAccount:$GCP_SERVICE_ACCOUNT@$GCP_PROJECT.iam.gserviceaccount.com" --role='roles/compute.storageAdmin'
```

## Enable billing
### Link it to the project we created on your account
** To do this you will need to go into the web console. Select your project from the drop down at the top of your screen. Then search for the billing section. **

### Enable the compute api
```
gcloud services enable compute.googleapis.com --project=$GCP_PROJECT
```

## BBL Commands
```
bbl plan
cp -r ../bosh-bootloader/plan-patches/bosh-lite-gcp/* .
bbl up
```

### Setup our environment variables for BOSH
```
eval "$(bbl print-env)"
```

** Depending on the version of cf-deployment you are using you may need to get a different stemcell version. **
```
bosh upload-stemcell https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3541.10
```

```
bosh -n -d cf deploy -v 'system_domain=sys.<your-domain>' -o ../cf-deployment/operations/bosh-lite.yml -o ../cf-deployment/operations/use-compiled-releases.yml --vars-store deployment-vars.yml ../cf-deployment/cf-deployment.yml
```

### Run this then look for the line with the internal ip of 10.0.0.6 and copy the external ip
```
gcloud compute instances list --project $GCP_PROJECT
```

### Go to your DNS provider and add the following entries
```
*.sys.<your-domain>.com - A to <external ip>
```

### Find the cf_admin password in ./deployment-vars.yml
** use the username `admin` and the password you found **
```
cf login -a api.sys.<yourdomain.com>
```

## Run CF commands! and profit!

## To tear down Billable resources in GCP
** You service account and your project will not be removed with this command **
```
bbl destroy
```
