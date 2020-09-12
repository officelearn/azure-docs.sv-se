---
title: Använda CSI-drivrutiner (container Storage Interface) för Azure-diskar på Azure Kubernetes service (AKS)
description: Lär dig hur du använder CSI-drivrutinerna (container Storage Interface) för Azure-diskar i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422088"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Använd driv rutinerna för Azure disk container Storage-gränssnittet (CSI) i Azure Kubernetes service (AKS) (för hands version)
Azure disk CSI-drivrutinen är en [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) -kompatibel driv rutin som används av AKS för att hantera livs cykeln för Azure-diskar. 

Container Storage-gränssnittet (CSI) är en standard för att exponera godtyckliga block-och fil lagrings system till arbets belastningar i behållare på Kubernetes. Genom att anta och använda CSI kan Azure Kubernetes service (AKS) skriva, distribuera och iterera plugin-program som visar nya eller förbättrade befintliga lagrings system i Kubernetes utan att behöva röra kärnan Kubernetes-koden och vänta på dess versions cykler.

Om du vill skapa ett AKS-kluster med stöd för CSI-drivrutiner, se [Aktivera CSI-drivrutiner för Azure-diskar och Azure Files på AKS](csi-storage-drivers.md).

>[!NOTE]
> *"I träd driv rutiner"* syftar på de aktuella lagrings driv rutiner som ingår i Kubernetes-koden och de nya CSI-drivrutinerna som är plugin-program.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Använda CSI-beständiga volymer (PV) med Azure-diskar 

En [beständig volym](concepts-storage.md#persistent-volumes) representerar en lagrings enhet som är etablerad för användning med Kubernetes poddar. En permanent volym kan användas av en eller flera poddar och kan vara dynamiskt eller statiskt etablerad. Den här artikeln visar hur du dynamiskt skapar permanenta volymer med Azure disks för användning av en enda Pod i ett Azure Kubernetes service-kluster (AKS). För statisk etablering, se [skapa och använda en volym med Azure-diskar manuellt](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Mer information om Kubernetes-volymer finns i [lagrings alternativ för program i AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Skapa Azure disk-PVs dynamiskt med hjälp av de inbyggda lagrings klasserna

En lagrings klass används för att definiera hur en lagrings enhet dynamiskt skapas med en permanent volym. Mer information om Kubernetes lagrings klasser finns i [Kubernetes Storage-klasser][kubernetes-storage-classes]. När du använder driv rutiner för lagring CSI på AKS finns det ytterligare två inbyggda `StorageClasses` som utnyttjar **Azure disk CSI Storage-drivrutiner**. De ytterligare klasserna för CSI-lagring skapas med klustret tillsammans med standard lagrings klasser i trädet.

>[!NOTE]
> *"I träd driv rutiner"* syftar på de aktuella lagrings driv rutiner som ingår i Kubernetes-koden eller CSI-drivrutinerna som är plugin-program.

- `managed-csi` – Använder Azure StandardSSD lokalt redundant lagring (LRS) för att skapa en hanterad disk.
- `managed-csi-premium` – Använder Azure Premium lokalt redundant lagring (LRS) för att skapa en hanterad disk. 

Anspråks principen i båda lagrings klasserna säkerställer att den underliggande Azure-disken tas bort när respektive permanent volym tas bort. Lagrings klasserna konfigurerar även de permanenta volymerna så att de kan utökas, du behöver bara redigera det beständiga volym anspråket med den nya storleken.

Om du vill utnyttja dessa lagrings klasser behöver du bara skapa ett [beständigt volym anspråk (PVC)](concepts-storage.md#persistent-volume-claims) och respektive POD som refererar till och utnyttjar dem. Ett permanent volym anspråk (PVC) används för att automatiskt etablera lagring baserat på en lagrings klass. En PVC kan använda en av de i förväg skapade lagrings klasserna eller en användardefinierad lagrings klass för att skapa en Azure-hanterad disk för önskad SKU och storlek. När du skapar en POD-definition anges beständigt volym anspråk för att begära önskad lagring.

Skapa ett exempel på pod och motsvarande beständiga volym anspråk med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

När pod är i körnings läge skapar du en ny fil med namnet `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Nu kan du kontrol lera att disken är korrekt monterad genom att köra kommandot nedan och verifiera att du ser `test.txt` filen i utdata: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Skapa en anpassad lagrings klass

Standard lagrings klasserna passar de vanligaste scenarierna, men inte alla. I vissa fall kanske du vill ha en egen lagrings klass anpassad med dina egna parametrar. För att exemplify har vi ett scenario där du kanske vill ändra `volumeBindingMode` . 

Standard lagrings klasserna använder en `volumeBindingMode: Immediate` sådan garanti som inträffar omedelbart när PersistentVolumeClaim har skapats. I de fall där noderna är topologi begränsade, till exempel med hjälp av Tillgänglighetszoner, skulle de beständiga volymerna vara bundna eller etablerade utan kunskaper om Pod schemaläggnings krav (i det här fallet vara i en speciell zon).

För att åtgärda det här scenariot kan du använda `volumeBindingMode: WaitForFirstConsumer` , vilket fördröjer bindningen och etableringen av en PersistentVolume tills en POD med hjälp av PersistentVolumeClaim skapas. På så sätt uppfyller PV och tillhandahålls i tillgänglighets zonen (eller annan topologi) som anges av Pod schemaläggnings begränsningar. 

Skapa en fil med namnet `sc-azuredisk-csi-waitforfirstconsumer.yaml` och klistra in manifestet nedan.
Lagrings klassen är samma som vår `managed-csi` lagrings klass men med en annan `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Skapa lagrings klassen med kommandot [kubectl Apply][kubectl-apply] och ange din `sc-azuredisk-csi-waitforfirstconsumer.yaml` fil:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Ögonblicks bilder av volymer

Driv rutinen för Azure disk CSI stöder skapande [av ögonblicks bilder av permanenta volymer](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Som en del av den här funktionen kan driv rutinen utföra antingen *fullständiga* eller [ *stegvisa* ögonblicks bilder](../virtual-machines/windows/disks-incremental-snapshots.md) beroende på vilket värde som anges i `incremental` parametern (som standard är det sant). 

Mer information om alla parametrar finns i [klass parametrar för volym ögonblicks bilder](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Skapa en ögonblicks bild av en volym

För att exemplify den här funktionen skapar du en [volym ögonblicks bild klass](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) med kommandot [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Nu ska vi skapa en [ögonblicks bild av en ögonblicks bild](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) från PVC: n som [skapas dynamiskt i början av den här självstudien](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Kontrol lera att ögonblicks bilden skapades korrekt:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Skapa en ny PVC som baseras på en ögonblicks bild av en volym

Du kan skapa en ny PVC baserat på en ögonblicks bild av en volym. Använd ögonblicks bilden som skapades i föregående steg och skapa en [ny PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) och [nya Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) för att använda den.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Nu ska vi kontrol lera att det är samma PVC som skapats innan genom att kontrol lera innehållet.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Som förväntat kan vi fortfarande se vår tidigare skapade `test.txt` fil.

## <a name="clone-volumes"></a>Klona volymer

En klonad volym definieras som en dubblett av en befintlig Kubernetes-volym. Mer information om kloning av volymer i Kubernetes finns i den konceptuella dokumentationen för [volym kloning](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

CSI-drivrutinen för Azure-diskar stöder volym kloning. För att demonstrera, skapar du en [klonad volym](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) av den [tidigare skapade](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` och [en ny Pod för att använda den](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Nu kan vi kontrol lera innehållet i den klonade volymen genom att köra nedan och bekräfta att vi fortfarande ser vår `test.txt` skapade fil.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Ändra storlek på en beständig volym (PV)

Du kan i stället begära en större volym för en PVC. Redigera PVC-objektet och ange en större storlek. Den här ändringen utlöser expansionen av den underliggande volymen som backar upp PersistentVolume. 

> [!NOTE] 
> En ny PersistentVolume skapas aldrig för att uppfylla kravet. I stället ändras storleken på en befintlig volym.

I AKS tillåter den inbyggda `managed-csi` lagrings klassen redan expansion, så du kan utnyttja den [PVC som skapades tidigare med den här lagrings klassen](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes). PVC: en begärde en 10Gi beständig volym, vi kan bekräfta att genom att köra:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> För närvarande stöder Azure disk CSI-driv rutinen bara storleks ändring av PVC: er utan poddar (och volymen har inte monterats till en speciell nod).

Det innebär att du kan ta bort Pod som skapades tidigare:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Nu ska vi expandera PVC: n genom att öka `spec.resources.requests.storage` fältet:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Nu ska vi kontrol lera att volymen är större:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> PVC: n visar inte den nya storleken förrän den har en pod som är kopplad till den igen.

Nu ska vi skapa en ny pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Och slutligen bekräfta storleken på PVC: n och i pod: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Windows-container

Driv rutinen för Azure disk CSI stöder också Windows-noder och behållare. Om du vill använda Windows-behållare följer du [själv studie kursen för Windows-behållare](windows-container-cli.md) för att lägga till en Windows-Node-pool.

När du har en Windows-Node-pool kan du nu bara utnyttja de inbyggda lagrings klasserna som `managed-csi` . Du kan distribuera ett exempel på en [Windows-baserad tillstånds känslig uppsättning](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) som sparar tidsstämplar i en fil `data.txt` genom att distribuera nedanstående med kommandot [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Nu kan du validera innehållet på volymen genom att köra:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder CSI-drivrutinen för Azure Files finns i [använda Azure Files med CSI-drivrutiner](azure-files-csi.md).
- Mer information om metod tips för lagring finns i [metod tips för lagring och säkerhets kopiering i Azure Kubernetes service (AKS)][operator-best-practices-storage]


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register