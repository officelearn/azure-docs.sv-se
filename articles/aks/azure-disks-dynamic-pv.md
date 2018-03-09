---
title: "Använd Azure med AKS"
description: "Använda Azure-diskarna med AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 36e25d7e5f1e5c6e1cf72442b73ac081810d216a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Beständiga volymer med Azure-diskar

En beständig volym representerar en typ av lagring som har etablerats för användning med Kubernetes skida. En beständig volym kan användas av en eller flera skida och kan etableras statiskt eller dynamiskt. Mer information om Kubernetes beständiga volymer finns [Kubernetes beständiga volymer][kubernetes-volumes].

Det här dokumentet beskriver beständiga volymer med Azure-diskar i ett kluster med Azure Container Service (AKS).

## <a name="built-in-storage-classes"></a>Inbyggda lagringsklasser

En lagringsklass används för att definiera hur en enhet för lagring skapas dynamiskt med en beständig volym. Mer information om Kubernetes lagringsklasser finns [Kubernetes lagringsklasser][kubernetes-storage-classes].

Varje AKS kluster innehåller två lagringsklasser som skapats i förväg, båda är konfigurerade för att fungera med Azure-diskarna. Den `default` lagring klassen etablerar en standard Azure-disken. Den `managed-premium` lagring klassen etablerar en premium Azure-disken. Om AKS noder i klustret använder premium-lagring, Välj den `managed-premium` klass.

Använd den [kubectl hämta sc] [ kubectl-get] kommandot för att se i förväg skapade lagringsklasser.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Skapa beständiga volym anspråk

Beständiga volym-anspråk (PVC) används för att automatiskt etablera lagring baseras på en lagringsklass. I det här fallet en PVC kan använda en i förväg skapade lagringsklasser för att skapa en standard- eller premium Azure hanterade diskar.

Skapa en fil med namnet `azure-premimum.yaml`, och kopiera följande manifestet.

Notera som den `managed-premium` lagringsklass har angetts i anteckningen och anspråket begär en disk `5GB` i storlek med `ReadWriteOnce` åtkomst. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Skapa beständiga volym anspråk med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

> [!NOTE]
> En Azure-disken kan endast monteras med åtkomst läge typen ReadWriteOnce, vilket gör den tillgänglig att endast en AKS nod. Om du behöver att dela en beständig volym över flera noder, kan du använda [Azure Files][azure-files-pvc].

## <a name="using-the-persistent-volume"></a>Med hjälp av beständiga volymens

När anspråket beständiga volymen har skapats och den disk som etablerats, en baljor kan skapas med åtkomst till disken. Följande manifestet skapar en baljor som använder beständiga volym anspråket `azure-managed-disk` Azure disken på den `/mnt/azure` sökväg. 

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

Skapa baljor med den [kubectl skapa] [ kubectl-create] kommando.

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Nu har du en körs baljor med din Azure-disken monterat i den `/mnt/azure` directory. Du kan se volymen montera vid inspektion av din baljor via `kubectl describe pod mypod`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes beständiga volymer med Azure-diskarna.

> [!div class="nextstepaction"]
> [Kubernetes plugin-program för Azure-diskar][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md