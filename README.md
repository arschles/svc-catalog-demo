# Service Catalog Demo

This repo provides a walkthrough of the Service Catalog using only Helm charts.
The walkthrough will install:

 * Service Catalog API Server and Controller
 * An S3 Provider which includes a CF Broker for S3 bucket management
 * An S3 Consumer which uses the Broker's bindings and uploads a file to S3

## Setup Instructions

Install a Kubernetes cluster and make sure Helm is ready (i.e. `helm init`).

### Install the Service Catalog

```console
helm install \
    --namespace=steward \
    --name=svc-catalog \
    --set registry=quay.io/kubernetes-service-catalog,version=3500924,storageType=tpr,debug=true,insecure=true,imagePullPolicy=IfNotPresent,globalNamespace=steward \
    ./svc-catalog
```

### Install the S3 Provider

```console
# provide aws creds that have full S3 bucket rights and full IAM rights
export STEWARD_ACCESS_KEY=<aws-access-key>
export STEWARD_SECRET_KEY=<aws-secret-key>

helm install \
    --namespace=steward \
    --name=s3-provider \
    --set AdminAwsAccessKeyId=${STEWARD_ACCESS_KEY},AdminAwsSecretAccessKey=${STEWARD_SECRET_KEY} \
    ./s3-provider
    
```

### Install the S3 Consumer

```console
helm install --namespace=steward --name=s3-consumer ./s3-consumer
```

### Cleanup

```console
helm delete --purge svc-catalog
helm delete --purge s3-provider
helm delete --purge s3-consumer
kubectl delete ns steward
```
