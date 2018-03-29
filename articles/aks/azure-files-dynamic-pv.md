---
title: Använda Azure-fil med AKS
description: Använda Azure-diskarna med AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78f447c96afe7955f115de4bbd28015cd231bb53
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Beständiga volymer med Azure-filer

En beständig volym representerar en typ av lagring som har etablerats för användning i ett Kubernetes kluster. En beständig volym kan användas av en eller flera skida och kan etableras statiskt eller dynamiskt. Det här dokumentet beskriver dynamisk etablering av en Azure-filresurs som Kubernetes beständiga volymer i ett AKS kluster. 

Mer information om Kubernetes beständiga volymer finns [Kubernetes beständiga volymer][kubernetes-volumes].

## <a name="create-storage-account"></a>Skapa lagringskonto

När dynamiskt etablerar en Azure-filresurs som en Kubernetes volym, kan storage-konto användas som den finns i samma resursgrupp som klustret AKS. Om det behövs kan du skapa ett lagringskonto i samma resursgrupp som klustret AKS. 

Använd för att identifiera rätt resursgruppen den [az grupplistan] [ az-group-list] kommando.

```azurecli-interactive
az group list --output table
```

Du söker efter en resursgrupp med ett namn som liknar `MC_clustername_clustername_locaton`, där klusternamn är namnet på klustret AKS och platsen är Azure-regionen där klustret har distribuerats.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Använd den [az storage-konto skapar] [ az-storage-account-create] kommando för att skapa lagringskontot. 

Med det här exemplet kan uppdatera `--resource-group` med namnet på resursgruppen och `--name` till ett valfritt namn.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Skapa storage-klass

En lagringsklass används för att definiera hur du skapar en Azure-filresursen. Ett visst lagringskonto kan anges i-klassen. Om ett lagringskonto har angetts en `skuName` och `location` måste anges, och alla lagringskonton i associerade resursgrupp utvärderas efter en matchning.

Mer information om Kubernetes lagringsklasser för Azure-filer finns [Kubernetes lagringsklasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera följande manifestet. Uppdatering av `storageAccount` med namnet på ditt mål-lagringskontot.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Skapa klassen lagring med de [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Skapa beständiga volym anspråk

Beständiga volym-anspråk (PVC) använder klassen lagringsobjektet att dynamiskt etablera ett Azure-filresursen. 

Följande Manifestet kan användas för att skapa en beständig volym anspråk `5GB` i storlek med `ReadWriteOnce` åtkomst.

Skapa en fil med namnet `azure-file-pvc.yaml` och kopiera följande manifestet. Se till att den `storageClassName` matchar klassen lagring som skapats i det sista steget.

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

Skapa beständiga volym anspråk med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

När slutförts, skapas filresursen. En Kubernetes hemlighet skapas också som innehåller anslutningsinformationen och autentiseringsuppgifterna.

## <a name="using-the-persistent-volume"></a>Med hjälp av beständiga volymens

Följande manifestet skapar en baljor som använder beständiga volym anspråket `azurefile` att montera den Azure-filresursen på den `/mnt/azure` sökväg.

Skapa en fil med namnet `azure-pvc-files.yaml`, och kopiera följande manifestet. Se till att den `claimName` matchar PVC som skapats i det sista steget.

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

Skapa baljor med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Nu har du en körs baljor med din Azure-disken monterat i den `/mnt/azure` directory. Du kan se volymen montera vid inspektion av din baljor via `kubectl describe pod mypod`.

## <a name="mount-options"></a>Monteringsalternativ
 
Standardvärden för fileMode och dirMode skiljer sig åt mellan Kubernetes versioner som beskrivs i följande tabell.
 
| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
