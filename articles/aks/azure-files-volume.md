---
title: Använda Azure-fil med AKS
description: Använda Azure-diskar med AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b35d0e33009f76e0b2d6f90c52c98ce5f317792d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436768"
---
# <a name="volumes-with-azure-files"></a>Volymer med Azure files

Behållarbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Azure files kan användas som den här externa datalager. Den här artikeln beskriver hur man använder Azure files som en Kubernetes-volym i Azure Kubernetes Service.

Mer information om Kubernetes volymer finns i [Kubernetes volymer][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresurs som en Kubernetes-volym, måste du skapa ett Azure Storage-konto och filresursen. Följande skript kan användas för att utföra dessa uppgifter. Anteckna eller uppdatera parametervärdena kan vissa av dessa behövs när du skapar Kubernetes-volym.

```azurecli-interactive
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Skapa Kubernetes-hemlighet

Kubernetes behöver autentiseringsuppgifter för att komma åt filresursen. Dessa autentiseringsuppgifter lagras i en [Kubernetes-hemlighet][kubernetes-secret], som refereras till när du skapar en Kubernetes-pod.

Använd följande kommando för att skapa hemligheten. Ersätt `STORAGE_ACCOUNT_NAME` med namnet på ditt lagringskonto, och `STORAGE_ACCOUNT_KEY` med lagringskontots åtkomstnyckel.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Montera en filresurs som volym

Montera Azure Files-resurs i din pod genom att konfigurera volymen i dess spec. Skapa en ny fil med namnet `azure-files-pod.yaml` med följande innehåll. Uppdatera `aksshare` med namnet på Azure Files dela.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Använda kubectl för att skapa en pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Nu har du en fungerande behållare med Azure-filresursen monteras i den `/mnt/azure` directory.  Du kan se volymen montera vid kontroll av din pod via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes volymer med Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-plugin-programmet för Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
