---
title: Använda Azure-fil med AKS
description: Använda Azure-diskar med AKS
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: ea77244d4b2e078c5eda716e94a97291350228f5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058515"
---
# <a name="persistent-volumes-with-azure-files"></a>Beständiga volymer med Azure files

En permanent volym är en del av lagring som har skapats för användning i ett Kubernetes-kluster. En permanent volym kan användas av en eller flera poddar och kan skapas dynamiskt eller statiskt. Den här dokumentet beskriver **skapas dynamiskt** av en Azure-filresurs som en permanent volym.

Mer information om Kubernetes finns i beständiga volymer, inklusive statiska skapa [Kubernetes beständiga volymer][kubernetes-volumes].

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

När du skapar en Azure-filresurs som en Kubernetes-volym dynamiskt, alla lagringskonton kan användas så länge som den är i AKS **noden** resursgrupp. Den här gruppen är som har den *MC_* prefix som har skapats genom att etablera resurser för AKS-klustret. Hämta resursgruppens namn med kommandot [az aks visa] [az-aks-visa].

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az storage-konto skapar] [ az-storage-account-create] kommando för att skapa lagringskontot.

Uppdatera `--resource-group` med namnet på resursgruppen som samlats in i det sista steget och `--name` till ett valfritt namn. Ange ditt eget unika lagringskontonamn:

```azurecli
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --sku Standard_LRS
```

> [!NOTE]
> Azure-filer för närvarande endast fungerar med standardlagring. Om du använder premiumlagring volymen inte går att etablera.

## <a name="create-a-storage-class"></a>Skapa en storage-klass

En lagringsklass används för att definiera hur en Azure-filresurs har skapats. Ett lagringskonto kan anges i-klassen. Om ett lagringskonto anges en *skuName* och *plats* måste anges, och alla lagringskonton i den associerade resursgruppen utvärderas efter en matchning. Mer information om Kubernetes lagringsklasser för Azure Files finns i [Kubernetes lagringsklasser][kubernetes-storage-classes].

Skapa en fil med namnet `azure-file-sc.yaml` och kopiera i följande exempel manifestet. Uppdatera den *storageAccount* värdet med namnet på ditt lagringskonto som skapades i föregående steg. Mer information om *mountOptions*, finns i den [monteringsalternativ] [ mount-options] avsnittet.

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
  storageAccount: mystorageaccount
```

Skapa klassen lagring med den [kubectl gäller] [ kubectl-apply] kommando:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Skapa en klustrad roll och bindning

AKS-kluster använder Kubernetes rollbaserad åtkomstkontroll (RBAC) till gränsen åtgärder som kan utföras. *Roller* definierar behörigheterna som ska tilldelas, och *bindningar* tillämpa dem till önskade användare. Dessa uppgifter kan tillämpas till ett visst namnområde eller över hela klustret. Mer information finns i [med RBAC-auktorisering][kubernetes-rbac].

Om du vill tillåta Azure-plattformen för att skapa de nödvändiga lagringsresurserna, skapa en *clusterrole* och *clusterrolebinding*. Skapa en fil med namnet `azure-pvc-roles.yaml` och kopiera följande YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Tilldela behörigheter med den [kubectl gäller] [ kubectl-apply] kommando:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett anspråk för permanent volym

Ett permanent volym-anspråk (PVC) använder klassen lagringsobjektet för att dynamiskt etablera en Azure-filresurs. Följande YAML kan användas för att skapa ett permanent volym-anspråk *5GB* i storlek med *ReadWriteMany* åtkomst. Mer information om åtkomstlägen som finns i den [Kubernetes permanent volym] [ access-modes] dokumentation.

Nu skapa en fil med namnet `azure-file-pvc.yaml` och kopiera följande YAML. Se till att den *storageClassName* matchar lagringsklass som skapats i det sista steget:

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

Skapa permanent volym-anspråk med den [kubectl gäller] [ kubectl-apply] kommando:

```console
kubectl apply -f azure-file-pvc.yaml
```

När klar skapas filresursen. En Kubernetes-hemlighet skapas också som innehåller anslutningsinformationen och autentiseringsuppgifterna. Du kan använda den [kubectl hämta] [ kubectl-get] kommando för att visa status för PVC: N:

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Använd permanent volym

Följande YAML skapar en pod som använder permanent volym-anspråket *azurefile* att montera Azure-filresursen på den */mnt/azure* sökväg.

Skapa en fil med namnet `azure-pvc-files.yaml`, och kopiera följande YAML. Se till att den *claimName* matchar PVC som skapats i det sista steget.

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

```console
kubectl apply -f azure-pvc-files.yaml
```

Nu har du en aktiv pod med din Azure-disken monteras i den */mnt/azure* directory. Den här konfigurationen kan visas vid kontroll av din pod via `kubectl describe pod mypod`. Följande komprimerade exempel på utdata visar volym monteras i behållaren:

```
Containers:
  myfrontend:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile2
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Monteringsalternativ

Standard *fileMode* och *dirMode* värden skiljer sig åt mellan Kubernetes-versioner som beskrivs i följande tabell.

| version | värde |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 eller senare | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 eller senare | 0755 |

Om du använder ett kluster av version 1.8.5 återställning eller större och vilket dynamiskt skapar permanent volym med en lagringsklass, monteringsalternativ kan anges på storage class-objektet. I följande exempel anges *0777*:

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

Om du använder ett kluster av version 1.8.5 återställning eller större och statiskt skapar objektet permanent volym, monteringsalternativ måste anges på den *PersistentVolume* objekt. Läs mer om hur du skapar en permanent volym statiskt [Statiska beständiga volymer][pv-static].

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

Om du använder ett kluster av version 1.8.0 - 1.8.4, en säkerhetskontext kan bara anges med den *användare* värdet *0*. Läs mer på Pod säkerhetskontext [konfigurera en säkerhetskontext][kubernetes-security-context].

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-plugin-programmet för Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
