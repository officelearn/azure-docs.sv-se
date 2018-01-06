---
title: "Använda Azure-fil med AKS"
description: "Använda Azure-diskarna med AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d468944883cca80946001724c38dd5ec9ba0d94f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Beständiga volymer med Azure files - dynamiska etablering

En beständig volym representerar en typ av lagring som har etablerats för användning i ett Kubernetes kluster. En beständig volym kan användas av en eller flera skida och kan etableras statiskt eller dynamiskt. Det här dokumentet beskriver dynamisk etablering av en Azure-filresurs som Kubernetes beständiga volymer i ett AKS kluster. 

Mer information om Kubernetes beständiga volymer finns [Kubernetes beständiga volymer][kubernetes-volumes].

## <a name="prerequisites"></a>Förutsättningar

När dynamiskt etablerar en Azure-filresurs som en Kubernetes volym, kan storage-konto användas som den finns i samma resursgrupp som klustret AKS. Om det behövs kan du skapa ett lagringskonto i samma resursgrupp som klustret AKS. 

Använd för att identifiera rätt resursgruppen den [az grupplistan] [ az-group-list] kommando.

```azurecli-interactive
az group list --output table
```

Följande visas exempel resursgrupper, båda är associerade med ett AKS-kluster. Resursgruppens namn med ett namn som *MC_myAKSCluster_myAKSCluster_eastus* innehåller klusterresurser AKS och är där storage-konto måste skapas. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

När resursgruppen har identifierats, skapa lagringskontot med den [az storage-konto skapar] [ az-storage-account-create] kommando.

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Skapa storage-klass

En lagringsklass används för att definiera hur en dynamiskt skapade beständiga volym är konfigurerat. Objekt som kontonamn för Azure storage, SKU och region definieras i klassen lagringsobjektet. Mer information om Kubernetes lagringsklasser finns [Kubernetes lagringsklasser][kubernetes-storage-classes].

I följande exempel anger storage-konto för SKU: N skriver `Standard_LRS` i den `eastus` region kan användas när du begär lagring. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Att använda ett visst lagringskonto i `storageAccount` parametern kan användas.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Skapa beständiga volym anspråk

En beständig volym anspråk använder klassen lagringsobjektet för att dynamiskt etablerar en typ av lagring. När du använder en Azure-filer, skapas en Azure-filresursen i lagringskontot valt eller som anges i klassen lagringsobjektet.

>  [!NOTE]
>   Kontrollera att ett lämpligt storage-konto har skapats före i samma resursgrupp som klustret AKS. Beständiga volym anspråket misslyckas att etablera Azure filresursen om ett lagringskonto inte är tillgänglig. 

Följande Manifestet kan användas för att skapa en beständig volym anspråk `5GB` i storlek med `ReadWriteOnce` åtkomst. Mer information om PVC Åtkomstlägen finns [Åtkomstlägen][access-modes].

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Med hjälp av beständiga volymens

När anspråket beständiga volymen har skapats och lagringen har etablerats, en baljor kan skapas med åtkomst till volymen. Följande manifestet skapar en baljor som använder beständiga volym anspråket `azurefile` att montera den Azure-filresursen på den `/var/www/html` sökväg. 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>Monteringsalternativ

Standardvärden för fileMode och dirMode skiljer sig åt mellan Kubernetes versioner som beskrivs i följande tabell. 

| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 eller senare | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 eller senare | 0755 |

Om du använder ett kluster av version 1.8.5 eller högre, monteringspunkter som alternativ kan anges för klass lagringsobjektet. Följande exempel anger `0777`. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Om du använder ett kluster av version 1.8.0 - 1.8.4, en säkerhetskontext kan anges med den `runAsUser` värdet `0`. Mer information om baljor säkerhetskontext finns [konfigurera en säkerhetskontext][kubernetes-security-context].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure-filer.

> [!div class="nextstepaction"]
> [Kubernetes plugin-program för Azure-filer][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create