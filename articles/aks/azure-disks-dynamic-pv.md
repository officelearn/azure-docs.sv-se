---
title: Skapa beständiga volymer med Azure Kubernetes Service
description: Lär dig hur du använder Azure-diskar för att skapa beständiga volymer för poddar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 34d3a5dbccf2cad7873bf6166e406c7c4817ac09
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158717"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Skapa beständiga volymer med Azure-diskar för Azure Kubernetes Service (AKS)

En permanent volym representerar en typ av lagring som har etablerats för användning med Kubernetes-poddar. En permanent volym kan användas av en eller flera poddar och kan etableras statiskt eller dynamiskt. Mer information om Kubernetes beständiga volymer finns i [Kubernetes beständiga volymer][kubernetes-volumes]. Den här artikeln visar hur du använder beständiga volymer med Azure-diskar i ett kluster i Azure Kubernetes Service (AKS).

> [!NOTE]
> En Azure-disk kan endast monteras med *åtkomstläge* typ *ReadWriteOnce*, vilket gör dem tillgängliga för en enskild AKS nod. Om du behöver att dela en permanent volym över flera noder, bör du använda [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Inbyggda lagringsklasser

En lagringsklass används för att definiera hur en enhet lagringsutrymme skapas dynamiskt med en permanent volym. Läs mer på Kubernetes lagringsklasser [Kubernetes lagringsklasser][kubernetes-storage-classes].

Varje AKS-kluster innehåller två färdiga lagringsklasser, båda är konfigurerade för att fungera med Azure-diskar. Den *standard* lagringsklass etablerar en standarddisk till Azure. Den *hanteras premium* lagringsklass etablerar en premium Azure-disk. Om AKS-noder i klustret använder premiumlagring, Välj den *hanteras premium* klass.

Använd den [kubectl hämta sc] [ kubectl-get] kommando för att se de förinställda lagringsklasser. I följande exempel visas den skapa lagringsklasser som är tillgängliga i ett AKS-kluster:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Beständig volym anspråk har angetts i GiB men Azure hanterade diskar faktureras av SKU: N för en viss storlek. Dessa SKU: er mellan 32GiB för S4 eller P4 diskar och 4TiB för S50 eller P50 diskar. Dessutom kan det dataflöde och IOPS-prestanda för en Premium managed disk beror på båda SKU och instansstorleken för noderna i AKS-klustret. Mer information finns i [priser och prestanda för Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Skapa ett anspråk för permanent volym

Ett permanent volym-anspråk (PVC) används för att automatiskt etablera lagring baserat på en lagringsklass. I det här fallet en PVC kan använda någon av de färdiga lagringsklasser för att skapa en standard- eller premium Azure hanterade diskar.

Skapa en fil med namnet `azure-premium.yaml`, och kopiera följande manifestet.

Anteckna som den *hanteras premium* storage klass har angetts i anteckningen och anspråket begär en disk *5GB* i storlek med *ReadWriteOnce* åtkomst.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Du kan skapa en disk som använder standard-lagring med `storageClassName: default` snarare än *hanteras premium*.

Skapa permanent volym-anspråk med den [kubectl gäller] [ kubectl-apply] kommandot och ange din *azure premium.yaml* fil:

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Använd permanent volym

När permanent volym-anspråket har skapats och den disk som har etablerats, en pod kan skapas med åtkomst till disken. Följande manifestet skapar en pod som använder permanent volym-anspråket *azure managed disk* att montera Azure-disken på den `/mnt/azure` sökväg.

Skapa en fil med namnet `azure-pvc-disk.yaml`, och kopiera följande manifestet.

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
        claimName: azure-managed-disk
```

Skapa en pod med den [kubectl gäller] [ kubectl-apply] kommando.

```console
kubectl apply -f azure-pvc-disk.yaml
```

Nu har du en aktiv pod med din Azure-disken monteras i den `/mnt/azure` directory. Den här konfigurationen kan visas vid kontroll av din pod via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure-diskar.

> [!div class="nextstepaction"]
> [Kubernetes-plugin för Azure-diskar][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
