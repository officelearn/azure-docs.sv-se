---
title: Skapa en volym för flera poddar dynamiskt i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en permanent volym dynamiskt med Azure-diskar för användning med flera samtidiga poddar i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 735be71faecb9882b13f6f536d43715139d0f4db
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342028"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamiskt skapa och använda en permanent volym med Azure-diskar i Azure Kubernetes Service (AKS)

En permanent volym representerar en typ av lagring som har etablerats för användning med Kubernetes-poddar. En permanent volym kan användas av en eller flera poddar och kan etableras statiskt eller dynamiskt. Den här artikeln visar hur du skapar dynamiskt beständiga volymer med Azure-diskar för användning av en enda pod i ett kluster i Azure Kubernetes Service (AKS).

> [!NOTE]
> En Azure-disk kan endast monteras med *åtkomstläge* typ *ReadWriteOnce*, vilket gör dem tillgängliga för endast en enda pod i AKS. Om du vill dela en permanent volym över flera poddar kan använda [Azure Files][azure-files-pvc].

Mer information om Kubernetes volymer finns i [lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.59 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="built-in-storage-classes"></a>Inbyggda lagringsklasser

En lagringsklass används för att definiera hur en enhet lagringsutrymme skapas dynamiskt med en permanent volym. Läs mer på Kubernetes lagringsklasser [Kubernetes lagringsklasser][kubernetes-storage-classes].

Varje AKS-kluster innehåller två färdiga lagringsklasser, båda är konfigurerade för att fungera med Azure-diskar:

* Den *standard* lagringsklass etablerar en standarddisk till Azure.
    * Standardlagring stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.
* Den *hanteras premium* lagringsklass etablerar en premium Azure-disk.
    * Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Om AKS-noder i klustret använder premiumlagring, Välj den *hanteras premium* klass.

Använd den [kubectl hämta sc] [ kubectl-get] kommando för att se de förinställda lagringsklasser. I följande exempel visas den skapa lagringsklasser som är tillgängliga i ett AKS-kluster:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Beständig volym anspråk har angetts i GiB men Azure hanterade diskar faktureras av SKU: N för en viss storlek. Dessa SKU: er mellan 32GiB för S4 eller P4 diskar och 32TiB för S80 eller P80 diskar (i förhandsversion). Dataflöde och IOPS-prestanda för en Premium managed disk beror på båda SKU och instansstorleken för noderna i AKS-klustret. Mer information finns i [priser och prestanda för Managed Disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Skapa ett anspråk för permanent volym

Ett permanent volym-anspråk (PVC) används för att automatiskt etablera lagring baserat på en lagringsklass. I det här fallet en PVC kan använda någon av de färdiga lagringsklasser för att skapa en standard- eller premium Azure hanterade diskar.

Skapa en fil med namnet `azure-premium.yaml`, och kopiera följande manifestet. Anspråket förfrågningar en disk med namnet `azure-managed-disk` dvs *5GB* i storlek med *ReadWriteOnce* åtkomst. Den *hanteras premium* storage klass har angetts som storage-klassen.

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
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Använd permanent volym

När permanent volym-anspråket har skapats och den disk som har etablerats, en pod kan skapas med åtkomst till disken. Följande manifestet skapar en grundläggande NGINX-pod som använder permanent volym-anspråk med namnet *azure managed disk* att montera Azure-disken på sökvägen `/mnt/azure`.

Skapa en fil med namnet `azure-pvc-disk.yaml`, och kopiera följande manifestet.

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
        claimName: azure-managed-disk
```

Skapa en pod med den [kubectl gäller] [ kubectl-apply] kommandot, som visas i följande exempel:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Nu har du en aktiv pod med din Azure-disken monteras i den `/mnt/azure` directory. Den här konfigurationen kan visas vid kontroll av din pod via `kubectl describe pod mypod`, enligt följande komprimerade exempel:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Säkerhetskopiera en permanent volym

Ta en ögonblicksbild av den hantera disken på volymen om du vill säkerhetskopiera data i din permanent volym. Du kan sedan använda den här ögonblicksbilden för att skapa en återställd disk och Anslut till poddar som ett sätt att återställa data.

Hämta först volymnamnet på med den `kubectl get pvc` kommandot, till exempel för PVC med namnet *azure managed disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Den här volymnamn utgör underliggande Azure diskens namn. Fråga för disk-ID och [az disk list] [ az-disk-list] och anger din PVC volymens namn som visas i följande exempel:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Använd disk-ID för att skapa en ögonblicksbild disk med [az snapshot skapa][az-snapshot-create]. I följande exempel skapas en ögonblicksbild med namnet *pvcSnapshot* i samma resursgrupp som AKS-klustret (*MC_myResourceGroup_myAKSCluster_eastus*). Du kan stöta på problem med behörighet om du skapar ögonblicksbilder och Återställ diskar i resursgrupper som AKS-klustret inte har åtkomst till.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Beroende på mängden data på disken, kan det ta några minuter att skapa ögonblicksbilden.

## <a name="restore-and-use-a-snapshot"></a>Återställa och använda en ögonblicksbild

Om du vill återställa disken och använder den med en Kubernetes-pod kan du använda ögonblicksbilden som källa när du skapar en disk med [az disk skapa][az-disk-create]. Den här åtgärden bevarar den ursprungliga resursen om du sedan behöver komma åt den ursprungliga dataögonblicksbilden. I följande exempel skapas en disk med namnet *pvcRestored* från ögonblicksbilden med namnet *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Om du vill använda den återställda disken med en pod, ange ID för disken i manifestet. Hämta diskens ID med den [az disk show] [ az-disk-show] kommando. I följande exempel hämtas disk-ID för *pvcRestored* skapade i föregående steg:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Skapa en pod-manifest med namnet `azure-restored.yaml` och ange disk URI som hämtades i föregående steg. I följande exempel skapas en grundläggande NGINX-webbserver med den återställda disken som monterats som en volym på */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Skapa en pod med den [kubectl gäller] [ kubectl-apply] kommandot, som visas i följande exempel:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Du kan använda `kubectl describe pod mypodrestored` att visa detaljer för en pod, till exempel följande komprimerade exempel som visar volyminformationen:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Nästa steg

Associerade metodtips finns [bästa praxis för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

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
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
