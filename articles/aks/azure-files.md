---
title: "Använda Azure-fil med AKS | Microsoft Docs"
description: "Använda Azure-diskarna med AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 11457e6556e6400d8f58f71c71ab1e790bcef8f1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Använda Azure-filer i Kubernetes

Behållare baserat program behöver ofta åtkomst och spara data i en volym på externa data. Azure-filer kan användas som den här externa datalager. Den här artikeln information med hjälp av Azure filer som Kubernetes volymer i Azure Container Service.

Mer information om Kubernetes volymer finns [Kubernetes volymer][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Skapa en filresurs

En befintlig Azure-resurs kan användas med Azure Container Service. Om du behöver skapa en använder du följande uppsättning kommandon.

Skapa en resursgrupp för Azure File resursen med det [az gruppen skapa] [ az-group-create] kommando. Resursgruppen för lagringskontot och Kubernetes klustret måste finnas i samma region.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd den [az storage-konto skapar] [ az-storage-create] kommando för att skapa ett Azure Storage-konto. Lagringskontonamnet måste vara unikt. Uppdatera värdet för den `--name` argumentet med ett unikt värde.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Använd den [az nycklar lagringskontolistan ] [ az-storage-key-list] kommando för att returnera nyckel för säkerhetslagring. Uppdatera värdet för den `--account-name` argumentet med unika lagringskontonamn.

Ta del av en av nyckelvärden, används i efterföljande steg.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Använd den [az lagringsresurs skapa] [ az-storage-share-create] kommando för att skapa Azure-filresursen. Uppdatering av `--account-key` värdet med ett värde som samlas in i det sista steget.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Skapa Kubernetes hemlighet

Kubernetes måste autentiseringsuppgifter för åtkomst till filresursen. I stället för att lagra Azure Storage-kontonamnet och nyckeln med varje baljor, lagras en gång i en [Kubernetes hemlighet] [ kubernetes-secret] och refereras av varje volym på Azure-filer. 

Värden i en hemlig Kubernetes-manifestet måste vara base64-kodad. Använd följande kommandon för att returnera kodade värden.

Koda först namnet på lagringskontot. Ersätt `storage-account` med namnet på ditt Azure storage-konto.

```azurecli-interactive
echo -n <storage-account> | base64
```

Därefter krävs åtkomstnyckeln för lagringskontot. Kör följande kommando för att returnera den kodade nyckeln. Ersätt `storage-key` med nyckel som samlas in i ett tidigare steg

```azurecli-interactive
echo -n <storage-key> | base64
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

Använd den [kubectl gäller] [ kubectl-apply] kommando för att skapa hemligheten.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montera filresursen som volym

Du kan montera filer för Azure-resurs i din baljor genom att konfigurera volymen i dess-specifikationen. Skapa en ny fil med namnet `azure-files-pod.yml` med följande innehåll. Uppdatera `share-name` dela med namnet på Azure-filer.

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
      shareName: <share-name>
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
> [Kubernetes plugin-program för Azure-filer](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create