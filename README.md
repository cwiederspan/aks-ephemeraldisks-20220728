# AKS Ephemeral Testing

A repo for testing ephemeral disk support in Azure Kubernetes.

## Create an AKS Cluster with Ephemeral Disks

```bash

BASENAME=cdw-aksephemeral-20220728
LOCATION=centralus

az group create --name $BASENAME --location $LOCATION

az aks create \
--name $BASENAME \
--resource-group $BASENAME \
-s Standard_D4ads_v5 \
--node-osdisk-type Ephemeral \
--node-count 1 \
--generate-ssh-keys


az aks get-credentials -n $BASENAME -g $BASENAME


```

```bash

az aks show \
  --name $BASENAME \
  --resource-group $BASENAME \
  --query 'agentPoolProfiles[].{name:name,osDiskType:osDiskType,kubeletDiskType:kubeletDiskType}' \
  --output table \
  --only-show-errors

```
