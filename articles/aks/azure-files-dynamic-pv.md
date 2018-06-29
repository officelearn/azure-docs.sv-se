---
title: Använda Azure-fil med AKS
description: Använda Azure-diskarna med AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84500791887194884e1ec7d15ddfbc169ba22517
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098353"
---
# <a name="persistent-volumes-with-azure-files"></a>Beständiga volymer med Azure-filer

En beständig volym är en typ av lagring som har skapats för användning i ett Kubernetes kluster. En beständig volym kan användas av en eller flera skida och kan skapas dynamiskt eller statiskt. Det här dokumentet beskriver **skapas dynamiskt** av en Azure-filresurs som en beständig volym.

Mer information om Kubernetes beständiga volymer, inklusive statiska skapa finns [Kubernetes beständiga volymer][kubernetes-volumes].

## <a name="create-storage-account"></a>Skapa lagringskonto

När du skapar en Azure-filresurs som en Kubernetes volym dynamiskt, storage-konto kan användas som den är i AKS **nod** resursgruppen. Detta är den med den `MC_` prefix som har skapats av etableringen av resurser för AKS klustret. Hämta resursgruppens namn med den [az resurs visa] [ az-resource-show] kommando.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az storage-konto skapar] [ az-storage-account-create] kommando för att skapa lagringskontot.

Uppdatera `--resource-group` med namnet på resursgruppen som samlats in i det sista steget, och `--name` till ett valfritt namn.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Azure Files fungerar endast med standardlagring. Om du använder premium-lagring kan inte etablera volymen.

## <a name="create-storage-class"></a>Skapa storage-klass

En lagringsklass används för att definiera hur du skapar en Azure-filresursen. Ett visst lagringskonto kan anges i-klassen. Om ett lagringskonto har angetts en `skuName` och `location` måste anges, och alla lagringskonton i associerade resursgrupp utvärderas efter en matchning.

Mer information om Kubernetes lagringsklasser för Azure-filer finns [Kubernetes lagringsklasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera följande manifestet. Uppdatering av `storageAccount` med namnet på ditt mål-lagringskontot. I avsnittet [monteringsalternativ] mer information på `mountOptions`.

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

Skapa klassen lagring med de [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Skapa beständiga volym anspråk

Beständiga volym-anspråk (PVC) använder klassen lagringsobjektet att dynamiskt etablera ett Azure-filresursen.

Följande YAML kan användas för att skapa en beständig volym anspråk `5GB` i storlek med `ReadWriteMany` åtkomst. Mer information om åtkomstlägen finns i [Kubernetes beständiga volym] [ access-modes] dokumentation.

Skapa en fil med namnet `azure-file-pvc.yaml` och kopiera följande YAML. Se till att den `storageClassName` matchar klassen lagring som skapats i det sista steget.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Skapa beständiga volym anspråk med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

När slutförts, skapas filresursen. En Kubernetes hemlighet skapas också som innehåller anslutningsinformationen och autentiseringsuppgifterna.

## <a name="using-the-persistent-volume"></a>Med hjälp av beständiga volymens

Följande YAML skapar en baljor som använder beständiga volym anspråket `azurefile` att montera den Azure-filresursen på den `/mnt/azure` sökväg.

Skapa en fil med namnet `azure-pvc-files.yaml`, och kopiera följande YAML. Se till att den `claimName` matchar PVC som skapats i det sista steget.

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
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Skapa baljor med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Nu har du en körs baljor med din Azure-disken monterat i den `/mnt/azure` directory. Den här konfigurationen kan visas vid inspektion av din baljor via `kubectl describe pod mypod`.

## <a name="mount-options"></a>Monteringsalternativ

Standardvärden för fileMode och dirMode skiljer sig åt mellan Kubernetes versioner som beskrivs i följande tabell.

| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 eller senare | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 eller senare | 0755 |

Om du använder ett kluster av version 1.8.5 eller högre och dynamiskt skapa persistant volymen med en lagringsklass, monteringsalternativ kan anges för klass lagringsobjektet. Följande exempel anger `0777`.

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

Om du använder ett kluster av version 1.8.5 eller högre och statiskt skapar objektet persistant volym, monteringsalternativ måste anges på den `PersistentVolume` objekt. Mer information om hur du skapar en persistant volym statiskt finns [Statiska beständiga volymer][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

Om du använder ett kluster av version 1.8.0 - 1.8.4, en säkerhetskontext kan anges med den `runAsUser` värdet `0`. Mer information om baljor säkerhetskontext finns [konfigurera en säkerhetskontext][kubernetes-security-context].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure-filer.

> [!div class="nextstepaction"]
> [Kubernetes plugin-program för Azure-filer][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
