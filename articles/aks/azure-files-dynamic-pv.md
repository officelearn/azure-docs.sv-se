---
title: Använda Azure-fil med AKS
description: Använda Azure-diskar med AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bac80e354a4d629bfbfc8207b9fed7c69c765839
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429275"
---
# <a name="persistent-volumes-with-azure-files"></a>Beständiga volymer med Azure files

En permanent volym är en del av lagring som har skapats för användning i ett Kubernetes-kluster. En permanent volym kan användas av en eller flera poddar och kan skapas dynamiskt eller statiskt. Den här dokumentet beskriver **skapas dynamiskt** av en Azure-filresurs som en permanent volym.

Mer information om Kubernetes finns i beständiga volymer, inklusive statiska skapa [Kubernetes beständiga volymer][kubernetes-volumes].

## <a name="create-storage-account"></a>Skapa lagringskonto

När du skapar en Azure-filresurs som en Kubernetes-volym dynamiskt, alla lagringskonton kan användas så länge som den är i AKS **noden** resursgrupp. Det här är som har den `MC_` prefix som har skapats genom att etablera resurser för AKS-klustret. Hämta resursgruppens namn med den [az resource show] [ az-resource-show] kommando.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az storage-konto skapar] [ az-storage-account-create] kommando för att skapa lagringskontot.

Uppdatera `--resource-group` med namnet på resursgruppen som samlats in i det sista steget och `--name` till ett valfritt namn.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Det fungerar endast för närvarande Azure Files med standardlagring. Om du använder premiumlagring kan inte etablera volymen.

## <a name="create-storage-class"></a>Skapa storage-klass

En lagringsklass används för att definiera hur en Azure-filresurs har skapats. Ett specifikt lagringskonto kan anges i-klassen. Om ett lagringskonto anges en `skuName` och `location` måste anges, och alla lagringskonton i den associerade resursgruppen utvärderas efter en matchning.

Mer information om Kubernetes lagringsklasser för Azure files finns i [Kubernetes lagringsklasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera följande manifestet. Uppdatera den `storageAccount` med namnet på ditt mål-lagringskontot. Se avsnittet [monteringsalternativ] för mer information på `mountOptions`.

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

Skapa klassen lagring med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Skapa permanent volym anspråk

Ett permanent volym-anspråk (PVC) använder klassen lagringsobjektet för att dynamiskt etablera en Azure-filresurs.

Följande YAML kan användas för att skapa ett permanent volym-anspråk `5GB` i storlek med `ReadWriteMany` åtkomst. Mer information om åtkomstlägen som finns i den [Kubernetes permanent volym] [ access-modes] dokumentation.

Skapa en fil med namnet `azure-file-pvc.yaml` och kopiera följande YAML. Se till att den `storageClassName` matchar lagringsklass som skapats i det sista steget.

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

Skapa permanent volym-anspråk med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

När klar skapas filresursen. En Kubernetes-hemlighet skapas också som innehåller anslutningsinformationen och autentiseringsuppgifterna.

## <a name="using-the-persistent-volume"></a>Med hjälp av permanent volym

Följande YAML skapar en pod som använder permanent volym-anspråket `azurefile` att montera Azure-filresursen på den `/mnt/azure` sökväg.

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

Skapa en pod med den [kubectl gäller] [ kubectl-apply] kommando.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Nu har du en aktiv pod med din Azure-disken monteras i den `/mnt/azure` directory. Den här konfigurationen kan visas vid kontroll av din pod via `kubectl describe pod mypod`.

## <a name="mount-options"></a>Monteringsalternativ

Standardvärden för fileMode och dirMode skiljer sig åt mellan Kubernetes-versioner som beskrivs i följande tabell.

| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 eller senare | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 eller senare | 0755 |

Om du använder ett kluster av version 1.8.5 återställning eller större och vilket dynamiskt skapar persistant volymen med en lagringsklass, monteringsalternativ kan anges på storage class-objektet. I följande exempel anges `0777`.

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

Om du använder ett kluster av version 1.8.5 återställning eller större och statiskt skapar objektet persistant volym, monteringsalternativ måste anges på den `PersistentVolume` objekt. Läs mer om hur du skapar en persistant volym statiskt [Statiska beständiga volymer][pv-static].

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

Om du använder ett kluster av version 1.8.0 - 1.8.4, en säkerhetskontext kan bara anges med den `runAsUser` värdet `0`. Läs mer på Pod säkerhetskontext [konfigurera en säkerhetskontext][kubernetes-security-context].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-plugin-programmet för Azure Files][kubernetes-files]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
