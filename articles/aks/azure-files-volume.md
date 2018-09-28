---
title: Skapa en statisk volym för flera poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du manuellt skapa en volym med Azure Files för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: e5518ebb2985635507368943774e6be803cfffa8
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409070"
---
# <a name="manually-create-and-use-an-azure-files-share-in-azure-kubernetes-service-aks"></a>Manuellt skapa och använda en Azure Files-resurs i Azure Kubernetes Service (AKS)

Behållarbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Om flera poddar behöver samtidig åtkomst till samma lagringsvolymen kan du använda Azure Files för att ansluta med den [Server Message Block (SMB) protokollet][smb-overview]. Den här artikeln visar hur du manuellt skapa en Azure Files-resurs och koppla den till en pod i AKS.

Mer information om Kubernetes volymer finns i [Kubernetes volymer][kubernetes-volumes].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.46 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda Azure Files som en Kubernetes-volym, måste du skapa ett Azure Storage-konto och filresursen. Följande skript skapar en resursgrupp med namnet *myAKSShare*, ett lagringskonto och en resurs för filer som heter *aksshare*:

```azurecli
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

Anteckna lagringskontonamn och nyckel visas i slutet av utdata från skriptet. Dessa värden krävs när du skapar Kubernetes-volym i något av följande steg.

## <a name="create-a-kubernetes-secret"></a>Skapa ett Kubernetes-hemlighet

Kubernetes behöver autentiseringsuppgifter för att få åtkomst till filresursen som skapades i föregående steg. Dessa autentiseringsuppgifter lagras i en [Kubernetes-hemlighet][kubernetes-secret], som refereras till när du skapar en Kubernetes-pod.

Använd den `kubectl create secret` kommando för att skapa hemligheten. I följande exempel skapas en delad med namnet *azure-secret*. Ersätt *STORAGE_ACCOUNT_NAME* med namnet på ditt lagringskonto visas i utdata från föregående steg, och *STORAGE_ACCOUNT_KEY* med din lagringsnyckel:

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montera filresursen som en volym

Om du vill montera Azure Files-resurs i din pod, konfigurerar du volymen i container-specifikationen. Skapa en ny fil med namnet `azure-files-pod.yaml` med följande innehåll. Om du har ändrat namnet på resursen filer eller hemligt namn måste du uppdatera den *shareName* och *secretName*. Om du vill kan du uppdatera den `mountPath`, vilket är den sökväg där filerna delar är monterad i en pod.

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

Använd den `kubectl` kommando för att skapa en pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Nu har du en aktiv pod med en Azure Files-resurs som är monterad på */mnt/azure*. Du kan använda `kubectl describe pod azure-files-pod` att verifiera resursen är monterad har.

## <a name="next-steps"></a>Nästa steg

Mer information om AKS-kluster interagerar med Azure Files, finns i den [Kubernetes-plugin-programmet för Azure Files][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
