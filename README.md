# How to deploy Prow on GKE?
  https://github.com/kubernetes/test-infra/blob/master/prow/getting_started_deploy.md
  
# CLI pre-requisites
  - [`kubectl`](https://kubernetes.io/docs/tasks/tools/)
  - [`gcloud`](https://cloud.google.com/sdk/docs/install)
  - [`tackle`](https://github.com/kubernetes/test-infra/blob/master/prow/getting_started_deploy.md#installing-tackle)

# QuickStart
  - Create a GKE Cluster (Autopilot/Custom)
  - Install Prow CRD
    - ```kubectl apply --server-side=true -f https://raw.githubusercontent.com/kubernetes/test-infra/master/config/prow/cluster/prowjob-crd/prowjob_customresourcedefinition.yaml```
  - Create a GCS Bucket
    - ```$ gcloud iam service-accounts create prow-gcs-publisher```
    - ```$ identifier="$(gcloud iam service-accounts list --filter 'name:prow-gcs-publisher' --format 'value(email)')"```
    - ```$ gsutil mb gs://prow-sandbox-utils/```
    - ```$ gsutil iam ch allUsers:objectViewer gs://prow-sandbox-utils```
    - ```$ gsutil iam ch "serviceAccount:${identifier}:objectAdmin" gs://prow-sandbox-utils```
    - ```$ gcloud iam service-accounts keys create --iam-account "${identifier}" service-account.json```
  - Clone this repo and substitute `github-token` secret & `hmac-token` token in **`starter-gcs.yaml`**\
    <sup>_(Assuming **Github_Org_Name**==`prow-sandbox-org` and **Prow_Domain**==`sandbox.dev`)_</sup>
  - Run ```tackle --skip-github``` then select the corresponding cluster and enter the path to **`starter-gcs.yaml`** when prompted
  - Create secrets in `test-pods` & `prow` namespace 
    - ```$ kubectl -n test-pods create secret generic gcs-credentials --from-file=service-account.json```
    - ```$ kubectl -n prow create secret generic gcs-credentials --from-file=service-account.json```
<!-- -->
  
<br>

**And voilà!! Prow instance is up and running :tada::tada:**
    
