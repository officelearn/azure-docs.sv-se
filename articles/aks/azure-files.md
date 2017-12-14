---
title: "Använda Azure-fil med AKS"
description: "Använda Azure-diskarna med AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c0aded35066b4dd819a754a663fdbbf0b0bf6feb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Använda Azure-filer i Kubernetes

Behållaren-baserade program behöver ofta åtkomst och spara data i en volym på externa data. Azure-filer kan användas som den här externa datalager. Den här artikeln information med hjälp av Azure filer som Kubernetes volymer i Azure Container Service.

Mer information om Kubernetes volymer finns [Kubernetes volymer][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresurs som en Kubernetes volym, måste du skapa ett Azure Storage-konto och filresursen. Följande skript kan användas för att utföra dessa uppgifter. Notera eller uppdatera parametervärden, vissa av dessa krävs när du skapar Kubernetes volymen.

```azurecli-interactive
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
```

## <a name="create-kubernetes-secret"></a>Skapa Kubernetes hemlighet

Kubernetes måste autentiseringsuppgifter för åtkomst till filresursen. Autentiseringsuppgifterna lagras i en [Kubernetes hemlighet][kubernetes-secret], som refereras till när du skapar en Kubernetes baljor.

När du skapar en Kubernetes hemliga måste hemliga värdena vara base64-kodad.

Koda först namnet på lagringskontot. Om det behövs, Ersätt `$AKS_PERS_STORAGE_ACCOUNT_NAME` med namnet på Azure-lagringskontot.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

Därefter koda lagringskontots åtkomstnyckel. Om det behövs, Ersätt `$STORAGE_KEY` med namnet på nyckeln för Azure storage-konto.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Skapa en fil med namnet `azure-secret.yml` och kopiera följande YAML. Uppdatering av `azurestorageaccountname` och `azurestorageaccountkey` värden med base64-kodade värdena som hämtas i det sista steget.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Använd den [kubectl skapa] [ kubectl-create] kommando för att skapa hemligheten.

```azurecli-interactive
kubectl create -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montera filresursen som volym

Du kan montera filer för Azure-resurs i din baljor genom att konfigurera volymen i dess-specifikationen. Skapa en ny fil med namnet `azure-files-pod.yml` med följande innehåll. Uppdatera `aksshare` dela med namnet på Azure-filer.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
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

Använd kubectl för att skapa en baljor.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Nu har du en behållare som körs med din Azure-filresursen monterat i den `/mnt/azure` directory. Du kan se volymen montera vid inspektion av din baljor via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes volymer med Azure-filer.

> [!div class="nextstepaction"]
> [Kubernetes plugin-program för Azure-filer][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
