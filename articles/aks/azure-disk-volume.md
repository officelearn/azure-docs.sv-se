---
title: Skapa en statisk volym för poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du manuellt skapa en volym med Azure-diskar för användning med en pod i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 02a863a4ddf59fb36c5f2ae7f3092896d2e1d860
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467535"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Manuellt skapa och använda en volym med Azure-diskar i Azure Kubernetes Service (AKS)

Behållarbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Om en enda pod behöver åtkomst till lagring, kan du använda Azure-diskar för att presentera en inbyggd volym för programmet. Den här artikeln visar hur du manuellt skapa en Azure-disk och koppla den till en pod i AKS.

> [!NOTE]
> En Azure-disk kan endast monteras till en enda pod i taget. Om du vill dela en permanent volym över flera poddar kan använda [Azure Files][azure-files-volume].

Mer information om Kubernetes volymer finns i [lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.59 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disk

När du skapar en Azure-disk för användning med AKS kan du skapa diskresursen i den **noden** resursgrupp. På så sätt kan komma åt och hantera diskresursen AKS-klustret. Om du i stället skapar disken i en separat resursgrupp, måste du ge Azure Kubernetes Service (AKS) tjänstens huvudnamn för klustret i `Contributor` rollen till diskens resursgrupp.

Den här artikeln är att skapa disken i resursgruppen noden. Hämta först resursgruppens namn med den [az aks show] [ az-aks-show] kommandot och lägga till den `--query nodeResourceGroup` frågeparameter. I följande exempel hämtar noden resursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nu skapa en disk med hjälp av den [az disk skapa] [ az-disk-create] kommando. Ange noden resursgruppens namn som hämtades i föregående kommando och sedan ett namn för diskresursen *myAKSDisk*. I följande exempel skapas en *20*GiB disk- och utdata ID: T för den disk som är skapade:

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-diskar faktureras av SKU: N för en viss storlek. Dessa SKU: er mellan 32GiB för S4 eller P4 diskar och 32TiB för S80 eller P80 diskar (i förhandsversion). Dataflöde och IOPS-prestanda för en Premium managed disk beror på både SKU: N och instansstorleken för noderna i AKS-klustret. Se [priser och prestanda för hanterade diskar][managed-disk-pricing-performance].

Resurs-ID för disken visas när kommandot har slutförts, enligt följande på Exempelutdata. Den här disk-ID används för att montera disken i nästa steg.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montera disken som volym

Om du vill montera Azure-disken till din pod, konfigurerar du volymen i container-specifikationen. Skapa en ny fil med namnet `azure-disk-pod.yaml` med följande innehåll. Uppdatera `diskName` med namnet på den disk som skapades i föregående steg, och `diskURI` skapar kommandot med disk-ID som visas i utdata från disken. Om du vill kan du uppdatera den `mountPath`, vilket är den sökväg där Azure-disken är monterad i en pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Använd den `kubectl` kommando för att skapa en pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Nu har du en aktiv pod med en Azure-disk monterad på `/mnt/azure`. Du kan använda `kubectl describe pod mypod` att kontrollera att disken är monterad har. Följande komprimerade exempel på utdata visar volym monteras i behållaren:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Nästa steg

Associerade metodtips finns [bästa praxis för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Mer information om AKS kluster interagera med Azure-diskar, finns i den [Kubernetes-plugin för Azure Disks][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
