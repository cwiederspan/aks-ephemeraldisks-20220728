# AKS Ephemeral Testing

A repo for testing ephemeral disk support in Azure Kubernetes.

## Create an AKS Cluster with Ephemeral Disks

```bash

BASENAME=cdw-aksephemeral-20220805
LOCATION=westus3

az group create --name $BASENAME --location $LOCATION

az aks create \
--name $BASENAME \
--resource-group $BASENAME \
--generate-ssh-keys \
--node-count 1 \
-s Standard_D4ads_v5 \
--node-osdisk-type Ephemeral \
# --node-osdisk-size 2040 \

az aks get-credentials -n $BASENAME -g $BASENAME

```

## Deploy the Local Storage Provisioner and Storage Class

Based on the documentation for the [AKS Local Volume Support](https://github.com/Azure/kubernetes-volume-drivers/tree/master/local).

```bash

# Create the StorageClass
# Take note of line #5 that names the StorageClass, in this case "local-disk".
kubectl apply -f local-pv-storageclass.yaml

# Install the local volume static provisioner
kubectl apply -f local-pv-provisioner-tempdisk.yaml

# Install the demo app
# Note line #22 that references the same StorageClass (local-disk) mentioned above
kubectl apply -f deployment.yaml

```

## Miscellaneous

```bash

az aks show \
  --name $BASENAME \
  --resource-group $BASENAME \
  --query 'agentPoolProfiles[].{name:name,osDiskType:osDiskType,kubeletDiskType:kubeletDiskType}' \
  --output table \
  --only-show-errors

```

## Cleanup

```bash

az group delete -n $BASENAME

```
