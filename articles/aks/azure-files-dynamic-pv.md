---
title: Skapa Azure-filresurs dynamiskt
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du dynamiskt skapar en beständig volym med Azure-filer för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803542"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Skapa och använda en beständig volym dynamiskt med Azure Files i Azure Kubernetes Service (AKS)

En beständig volym representerar en lagringsdel som har etablerats för användning med Kubernetes poddar. En beständig volym kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Om flera poddar behöver samtidig åtkomst till samma lagringsvolym kan du använda Azure Files för att ansluta med [SMB-protokollet (Server Message Block).][smb-overview] Den här artikeln visar hur du dynamiskt skapar en Azure-filresurs för användning av flera poddar i ett AKS-kluster (Azure Kubernetes Service).

Mer information om Kubernetes volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-a-storage-class"></a>Skapa en lagringsklass

En lagringsklass används för att definiera hur en Azure-filresurs skapas. Ett lagringskonto skapas automatiskt i [nodresursgruppen][node-resource-group] för användning med lagringsklassen för att lagra Azure-filresurserna. Välj följande [Azure-lagringsredundans][storage-skus] för *skuName:*

* *Standard_LRS* - standard lokalt redundant lagring (LRS)
* *Standard_GRS* - geoupptundret standardlagring (GRS)
* *Standard_ZRS* - standardzon redundant lagring (ZRS)
* *Standard_RAGRS* - geo redundant lagring av läsåtkomst (RA-GRS)
* *Premium_LRS* - premium lokalt redundant lagring (LRS)
* *Premium_ZRS* - premiumzon redundant lagring (GRS)

> [!NOTE]
> Azure Files stöder premiumlagring i AKS-kluster som kör Kubernetes 1.13 eller senare, minsta premiumfilresurs är 100 GB

Mer information om Kubernetes lagringsklasser för Azure-filer finns i [Kubernetes Storage Classes][kubernetes-storage-classes].

Skapa en `azure-file-sc.yaml` fil med namnet och kopiera i följande exempelmanifest. Mer information om *mountOptions*finns i avsnittet [Montera alternativ.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Skapa lagringsklassen med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volymanspråk

Ett beständigt volymanspråk (PVC) använder lagringsklassobjektet för att dynamiskt etablera en Azure-filresurs. Följande YAML kan användas för att skapa ett beständigt volymanspråk *5 GB* i storlek med *ReadWriteMany-åtkomst.* Mer information om åtkomstlägen finns i [kubernetes beständiga][access-modes] volymdokumentation.

Nu skapa en `azure-file-pvc.yaml` fil som heter och kopiera i följande YAML. Kontrollera att *storageClassName* matchar lagringsklassen som skapades i det sista steget:

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

> [!NOTE]
> Om du använder *Premium_LRS* sku för din lagringsklass måste minimivärdet för *lagring* vara *100Gi*.

Skapa det beständiga volymanspråket med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

När filresursen är klar skapas den. En Kubernetes hemlighet skapas också som innehåller anslutningsinformation och autentiseringsuppgifter. Du kan använda kommandot [kubectl get][kubectl-get] för att visa status för PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Använd den beständiga volymen

Följande YAML skapar en pod som använder den beständiga volymen anspråk *azurefile* att montera Azure-filresursen på */mnt/azure* sökvägen. För Windows Server-behållare (för närvarande i förhandsversionen i AKS) anger du en *mountPath* med hjälp av Windows-sökvägskonventionen, till exempel *"D:".*

Skapa en `azure-pvc-files.yaml`fil med namnet och kopiera i följande YAML. Kontrollera att *anspråksnamnet* matchar DEN PVC som skapades i det sista steget.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Skapa podden med kommandot [kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Du har nu en pod som körs med din Azure Files-resurs monterad i katalogen */mnt/azure.* Den här konfigurationen kan ses `kubectl describe pod mypod`när du inspekterar din pod via . Följande kondenserade exempelutgång visar volymen som är monterad i behållaren:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Monteringsalternativ

Standardvärdet för *fileMode* och *dirMode* är *0777* för Kubernetes version 1.13.0 och senare. Om du dynamiskt skapar den beständiga volymen med en lagringsklass kan monteringsalternativ anges på lagringsklassobjektet. Följande exempel anger *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Nästa steg

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Läs mer om Kubernetes beständiga volymer med Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes plugin för Azure-filer][kubernetes-files]

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
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
