---
title: Skapa en statisk volym för poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en volym manuellt med Azure-diskar för användning med en pod i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 17795ae696c0d710f099a5c21aa754fc925953ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047948"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Skapa och använda en volym manuellt med Azure-diskar i Azure Kubernetes Service (AKS)

Behållarbaserade program behöver ofta komma åt och bevara data i en extern datavolym. Om en enda pod behöver åtkomst till lagring kan du använda Azure-diskar för att presentera en inbyggd volym för programanvändning. Den här artikeln visar hur du skapar en Azure-disk manuellt och bifogar den till en pod i AKS.

> [!NOTE]
> En Azure-disk kan bara monteras på en enda pod åt gången. Om du behöver dela en beständig volym över flera poddar använder du [Azure Files][azure-files-volume].

Mer information om Kubernetes volymer finns i [Lagringsalternativ för program i AKS][concepts-storage].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disk

När du skapar en Azure-disk som kan användas med AKS kan du skapa diskresursen i **nodresursgruppen.** Med den här metoden kan AKS-klustret komma åt och hantera diskresursen. Om du i stället skapar disken i en separat resursgrupp måste du ge Azure Kubernetes Service (AKS) -tjänstens huvudnamn för klustret `Contributor` rollen till diskens resursgrupp. Du kan också använda den systemtilldelade hanterade identiteten för behörigheter i stället för tjänstens huvudnamn. Mer information finns i [Använda hanterade identiteter](use-managed-identity.md).

Skapa disken i nodresursgruppen för den här artikeln. Hämta först resursgruppsnamnet med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppnamnet *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en disk med kommandot [az disk create.][az-disk-create] Ange namnet på nodresursgruppen som erhölls i föregående kommando och sedan ett namn på diskresursen, till exempel *myAKSDisk*. I följande exempel skapas en *20*GiB-disk och diskens ID har skapats när den har skapats. Om du behöver skapa en disk som kan användas med Windows Server-behållare (för närvarande i förhandsversion i AKS) lägger du till `--os-type windows` parametern för att formatera disken korrekt.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-diskar faktureras av SKU för en viss storlek. Dessa SKU:er sträcker sig från 32GiB för S4- eller P4-diskar till 32TiB för S80- eller P80-diskar (i förhandsversion). Dataflödet och IOPS-prestanda för en Premium-hanterad disk beror på både SKU och instansstorleken för noderna i AKS-klustret. Se [Priser och prestanda för hanterade diskar][managed-disk-pricing-performance].

Diskresurs-ID visas när kommandot har slutförts, vilket visas i följande exempelutdata. Det här disk-ID:et används för att montera disken i nästa steg.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montera disk som volym

Om du vill montera Azure-disken i din pod konfigurerar `azure-disk-pod.yaml` du volymen i behållarspecifikationen. Uppdatera `diskName` med namnet på disken som skapades `diskURI` i föregående steg och med disk-ID som visas i utdata från kommandot disk skapa. Om du vill `mountPath`kan du uppdatera den sökväg där Azure-disken är monterad i podden. För Windows Server-behållare (för närvarande i förhandsversionen i AKS) anger du en *mountPath* med hjälp av Windows-sökvägskonventionen, till exempel *"D:".*

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

Använd `kubectl` kommandot för att skapa podden.

```console
kubectl apply -f azure-disk-pod.yaml
```

Du har nu en pod som körs `/mnt/azure`med en Azure-disk monterad på . Du kan `kubectl describe pod mypod` använda för att kontrollera att disken är monterad. Följande kondenserade exempelutgång visar volymen som är monterad i behållaren:

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

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

Mer information om AKS-kluster interagerar med Azure-diskar finns i [kubernetes plugin för Azure Disks][kubernetes-disks].

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
