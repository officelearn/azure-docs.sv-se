---
title: Integrera Azure NetApp-filer med Azure Kubernetes-tjänsten
description: Lär dig hur du integrerar Azure NetApp-filer med Azure Kubernetes Service
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273754"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrera Azure NetApp-filer med Azure Kubernetes-tjänsten

[Azure NetApp Files][anf] är en tjänst med hög prestanda med hög prestanda och fillagring med datapriser som körs på Azure. Den här artikeln visar hur du integrerar Azure NetApp-filer med Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

> [!IMPORTANT]
> Aks-klustret måste också finnas [i en region som stöder Azure NetApp-filer][anf-regions].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du använder Azure NetApp-filer:

* Azure NetApp-filer är endast tillgängliga [i valda Azure-regioner][anf-regions].
* Innan du kan använda Azure NetApp-filer måste du beviljas åtkomst till Azure NetApp Files-tjänsten. Om du vill ansöka om åtkomst kan du använda [formuläret för att skicka in inlämning av Azure NetApp Files.][anf-waitlist] Du kan inte komma åt Azure NetApp Files-tjänsten förrän du får det officiella bekräftelsemeddelandet från Azure NetApp Files-teamet.
* Din Azure NetApp Files-tjänst måste skapas i samma virtuella nätverk som AKS-klustret.
* Efter den första distributionen av ett AKS-kluster stöds endast statisk etablering för Azure NetApp-filer.
* Om du vill använda dynamisk etablering med Azure NetApp-filer installerar och konfigurerar du [NetApp Trident](https://netapp-trident.readthedocs.io/) version 19.07 eller senare.

## <a name="configure-azure-netapp-files"></a>Konfigurera Azure NetApp-filer

> [!IMPORTANT]
> Innan du kan registrera *Microsoft.NetApp-resursleverantören* måste du fylla i formuläret för att skicka [in skicka in skicka in skicka in skicka in skicka in skicka in azure netapp-filer][anf-waitlist] för din prenumeration. Du kan inte registrera resursen tills du får den officiella bekräftelsemeddelandet från Azure NetApp Files-teamet.

Registrera resursleverantören *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Detta kan ta lite tid att slutföra.

När du skapar ett Azure NetApp-konto för användning med AKS måste du skapa kontot i **nodresursgruppen.** Hämta först resursgruppsnamnet med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klustret *myAKSCluster* i resursgruppnamnet *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Skapa ett Azure NetApp-filkonto i **nodresursgruppen** och samma region som AKS-klustret med [az netappfiles-konto skapa][az-netappfiles-account-create]. I följande exempel skapas ett konto med namnet *myaccount1* i *MC_myResourceGroup_myAKSCluster_eastus* resursgrupp och *eastus-regionen:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Skapa en ny kapacitetspool med hjälp av [az netappfiles pool create][az-netappfiles-pool-create]. I följande exempel skapas en ny kapacitetspool med namnet *mypool1* med 4 TB i storlek och Premium-tjänstnivå: *Premium*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Skapa ett undernät som [delegerar till Azure NetApp-filer][anf-delegate-subnet] med [az-nätverksnätsundernät skapa][az-network-vnet-subnet-create]. *Det här undernätet måste finnas i samma virtuella nätverk som AKS-klustret.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Skapa en volym med hjälp av [az netappfiles volym skapa][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Skapa PersistentVolume

Lista information om din volym med [az netappfiles volym show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Skapa `pv-nfs.yaml` en definierande beständigvolym. Ersätt `path` med *creationToken* och `server` med *ipAddress* från föregående kommando. Ett exempel:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Uppdatera *servern* och *sökvägen* till värdena för NFS-volymen (Network File System) som du skapade i föregående steg. Skapa PersistentVolume med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Kontrollera att *status* för PersistentVolume är *tillgänglig* med kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Skapa PersistentVolumeClaim

Skapa `pvc-nfs.yaml` en definierande beständigvolym. Ett exempel:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Skapa PersistentVolumeClaim med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Kontrollera att *status* för PersistentVolumeClaim är *bunden* med kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montera med en kapsel

Skapa `nginx-nfs.yaml` en definierande pod som använder PersistentVolumeClaim. Ett exempel:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Skapa podden med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Kontrollera att podden *körs* med kommandot [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Kontrollera att volymen har monterats i podden med hjälp av `df -h` [kubectl exec][kubectl-exec] för att ansluta till pod sedan för att kontrollera om volymen är monterad.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure NetApp-filer finns i [Vad är Azure NetApp-filer][anf]. Mer information om hur du använder NFS med AKS finns i [Manuellt skapa och använda en NFS -volym (Network File System) Linux Server med Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
