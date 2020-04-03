---
title: Skapa och använda en beständig volym dynamiskt med Azure-diskar i Azure Kubernetes Service (AKS)
description: Lär dig hur du dynamiskt skapar en beständig volym med Azure-diskar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: f16a6134b1d2065668952ea11c0cab7398a3559a
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617476"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använda en beständig volym dynamiskt med Azure-diskar i Azure Kubernetes Service (AKS)

En beständig volym representerar en lagringsdel som har etablerats för användning med Kubernetes poddar. En beständig volym kan användas av en eller flera poddar och kan etableras dynamiskt eller statiskt. Den här artikeln visar hur du dynamiskt skapar beständiga volymer med Azure-diskar för användning av en enda pod i ett AKS-kluster (Azure Kubernetes Service).

> [!NOTE]
> En Azure-disk kan bara monteras med *åtkomstlägestypen* *ReadWriteOnce*, vilket gör den tillgänglig för endast en enda pod i AKS. Om du behöver dela en beständig volym över flera poddar använder du [Azure Files][azure-files-pvc].

Mer information om Kubernetes volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="built-in-storage-classes"></a>Inbyggda lagringsklasser

En lagringsklass används för att definiera hur en lagringsenhet skapas dynamiskt med en beständig volym. Mer information om Kubernetes lagringsklasser finns i [Kubernetes lagringsklasser][kubernetes-storage-classes].

Varje AKS-kluster innehåller två förskapade lagringsklasser, båda konfigurerade för att fungera med Azure-diskar:

* Standardlagringsklassen etablerar en standard Azure-disk. *default*
    * Standardlagring backas upp av hårddiskar och ger kostnadseffektiv lagring samtidigt som den utförs. Standarddiskar är idealiska för en kostnadseffektiv utvecklings- och testarbetsbelastning.
* Lagringsklassen *med hanterad premium* har en premium Azure-disk.
    * Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. Om AKS-noderna i klustret använder premiumlagring väljer du klassen *managed-premium.*
    
Dessa standardlagringsklasser tillåter inte att du uppdaterar volymstorleken när den har skapats. Om du vill aktivera den här möjligheten lägger du till *allowVolumeExpansion: true-raden* i en av standardlagringsklasserna eller skapar en egen anpassad lagringsklass. Du kan redigera en befintlig `kubectl edit sc` lagringsklass med kommandot. Mer information om lagringsklasser och skapa egna finns i [Lagringsalternativ för program i AKS][storage-class-concepts].

Använd [kommandot kubectl get sc][kubectl-get] för att se de förskapade lagringsklasserna. I följande exempel visas de förskapade lagringsklasser som är tillgängliga i ett AKS-kluster:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Beständiga volymanspråk anges i GiB men Azure-hanterade diskar faktureras av SKU för en viss storlek. Dessa SKU:er sträcker sig från 32GiB för S4- eller P4-diskar till 32TiB för S80- eller P80-diskar (i förhandsversion). Dataflödet och IOPS-prestanda för en Premium-hanterad disk beror på både SKU och instansstorleken för noderna i AKS-klustret. Mer information finns i [Priser och prestanda för hanterade diskar][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Skapa ett beständigt volymanspråk

Ett beständigt volymanspråk (PVC) används för att automatiskt etablera lagring baserat på en lagringsklass. I det här fallet kan en PVC använda en av de förskapade lagringsklasserna för att skapa en standard- eller premium-Azure-hanterad disk.

Skapa en `azure-premium.yaml`fil med namnet och kopiera i följande manifest. Anspråket begär en `azure-managed-disk` disk med namnet *5 GB* i storlek med *ReadWriteOnce-åtkomst.* Lagringsklassen *för hanterad premium* anges som lagringsklass.

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
> Om du vill skapa en `storageClassName: default` disk som använder standardlagring använder du i stället *för hanterad premium*.

Skapa det beständiga volymanspråket med kommandot [kubectl apply][kubectl-apply] och ange filen *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Använd den beständiga volymen

När det beständiga volymanspråket har skapats och disken har etablerats kan en pod skapas med åtkomst till disken. Följande manifest skapar en grundläggande NGINX-pod som använder det beständiga volymanspråket `/mnt/azure`med namnet *azure-managed-disk* för att montera Azure-disken i sökvägen . För Windows Server-behållare (för närvarande i förhandsversionen i AKS) anger du en *mountPath* med hjälp av Windows-sökvägskonventionen, till exempel *"D:".*

Skapa en `azure-pvc-disk.yaml`fil med namnet och kopiera i följande manifest.

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

Skapa podden med kommandot [kubectl apply,][kubectl-apply] som visas i följande exempel:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Du har nu en pod som körs `/mnt/azure` med din Azure-disk monterad i katalogen. Den här konfigurationen kan ses `kubectl describe pod mypod`vid inspektion av din pod via , som visas i följande komprimerade exempel:

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

## <a name="back-up-a-persistent-volume"></a>Säkerhetskopiera en beständig volym

Om du vill säkerhetskopiera data i den beständiga volymen tar du en ögonblicksbild av den hanterade disken för volymen. Du kan sedan använda den här ögonblicksbilden för att skapa en återställd disk och ansluta till poddar som ett sätt att återställa data.

Hämta först volymnamnet med `kubectl get pvc` kommandot, till exempel för PVC med namnet *azure-managed-disk:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Det här volymnamnet utgör det underliggande Azure-disknamnet. Fråga efter disk-ID med [az-disklista][az-disk-list] och ange ditt PVC-volymnamn, som visas i följande exempel:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Använd disk-ID för att skapa en ögonblicksbild disk med [az snapshot skapa][az-snapshot-create]. I följande exempel skapas en ögonblicksbild med namnet *pvcSnapshot* i samma resursgrupp som AKS-klustret (*MC_myResourceGroup_myAKSCluster_eastus*). Behörighetsproblem kan uppstå om du skapar ögonblicksbilder och återställer diskar i resursgrupper som AKS-klustret inte har åtkomst till.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Beroende på mängden data på disken kan det ta några minuter att skapa ögonblicksbilden.

## <a name="restore-and-use-a-snapshot"></a>Återställa och använda en ögonblicksbild

Om du vill återställa disken och använda den med en Kubernetes pod använder du ögonblicksbilden som källa när du skapar en disk med [az disk create][az-disk-create]. Den här åtgärden bevarar den ursprungliga resursen om du sedan behöver komma åt den ursprungliga dataögonblicksbilden. I följande exempel skapas en disk med namnet *pvcRestored* från ögonblicksbilden med namnet *pvcSnapshot:*

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Om du vill använda den återställda disken med en pod anger du diskens ID i manifestet. Hämta disk-ID med kommandot [az disk show.][az-disk-show] I följande exempel visas disk-ID för *pvcRestored* som skapats i föregående steg:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Skapa ett pod-manifest med namnet `azure-restored.yaml` och ange disk-URI som erhölls i föregående steg. I följande exempel skapas en grundläggande NGINX-webbserver, med den återställda disken monterad som en volym på */mnt/azure:*

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

Skapa podden med kommandot [kubectl apply,][kubectl-apply] som visas i följande exempel:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Du kan `kubectl describe pod mypodrestored` använda för att visa information om podden, till exempel följande komprimerade exempel som visar volyminformationen:

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

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Läs mer om Kubernetes beständiga volymer med Azure-diskar.

> [!div class="nextstepaction"]
> [Kubernetes plugin för Azure-diskar][azure-disk-volume]

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
[storage-class-concepts]: concepts-storage.md#storage-classes
