---
title: SSH till AKS-klusternoder (Azure Kubernetes Service)
description: Lär dig hur du skapar en SSH-anslutning med AKS-klusternoder (Azure Kubernetes Service) för felsökning och underhåll.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593639"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med SSH till AKS-klusternoder (Azure Kubernetes Service) vid underhåll eller felsökning

Under hela livscykeln för AKS-klustret (Azure Kubernetes Service) kan du behöva komma åt en AKS-nod. Den här åtkomsten kan vara för underhåll, logginsamling eller andra felsökningsåtgärder. Du kan komma åt AKS-noder med SSH, inklusive Windows Server-noder (för närvarande i förhandsversion i AKS). Du kan också [ansluta till Windows Server-noder med rdp-anslutningar (Remote Desktop Protocol).][aks-windows-rdp] Av säkerhetsskäl exponeras inte AKS-noderna för internet. Om du vill SSH till AKS-noderna använder du den privata IP-adressen.

Den här artikeln visar hur du skapar en SSH-anslutning med en AKS-nod med hjälp av deras privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Som standard hämtas SSH-nycklar eller genereras och läggs sedan till noder när du skapar ett AKS-kluster. Den här artikeln visar hur du anger olika SSH-nycklar än SSH-nycklarna som användes när du skapade AKS-klustret. Artikeln visar också hur du bestämmer den privata IP-adressen för din nod och ansluter till den med SSH. Om du inte behöver ange en annan SSH-nyckel kan du hoppa över steget för att lägga till den offentliga SSH-nyckeln i noden.

Den här artikeln förutsätter också att du har en SSH-nyckel. Du kan skapa en SSH-nyckel med [macOS eller Linux][ssh-nix] eller [Windows][ssh-windows]. Om du använder PuTTY Gen för att skapa nyckelparet sparar du nyckelparet i ett OpenSSH-format i stället för det privata nyckelformatet För närvarande PuTTy (.ppk-fil).

Du behöver också Azure CLI version 2.0.64 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurera setbaserade AKS-kluster för virtuell datorskala för SSH-åtkomst

Om du vill konfigurera den inställda virtuella datorn-skalan för SSH-åtkomst letar du reda på namnet på klustrets skalningsuppsättning för virtuella datorer och lägger till den offentliga SSH-nyckeln i den skalningsuppsättningen.

Använd kommandot [az aks visa][az-aks-show] för att hämta resursgruppnamnet för AKS-klustret och sedan kommandot [az vmss list][az-vmss-list] för att få namnet på din skalningsuppsättning.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

I exemplet ovan tilldelas namnet på klusterresursgruppen för *myAKSCluster* i *myResourceGroup* till *CLUSTER_RESOURCE_GROUP*. I exemplet används sedan *CLUSTER_RESOURCE_GROUP* för att lista skalningsuppsättningens namn och tilldela det till *SCALE_SET_NAME*.

> [!IMPORTANT]
> För närvarande bör du bara uppdatera dina SSH-nycklar för dina setbaserade AKS-kluster för virtuell datorskala med Azure CLI.
> 
> För Linux-noder kan SSH-nycklar för närvarande endast läggas till med Azure CLI. Om du vill ansluta till en Windows Server-nod med SSH använder du SSH-tangenterna som angavs när du skapade AKS-klustret och hoppar över nästa uppsättning kommandon för att lägga till den offentliga SSH-nyckeln. Du behöver fortfarande IP-adressen för den nod som du vill felsöka, vilket visas i det slutliga kommandot i det här avsnittet. Du kan också [ansluta till Windows Server-noder med rdp-anslutningar (Remote Desktop Protocol)][aks-windows-rdp] i stället för att använda SSH.

Om du vill lägga till SSH-nycklar i noderna i en skalningsuppsättning för virtuella datorer använder du [tilläggsuppsättningen az vmss][az-vmss-extension-set] och [az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

I exemplet ovan används *variablerna CLUSTER_RESOURCE_GROUP* och *SCALE_SET_NAME* från tidigare kommandon. I exemplet ovan används också *~/.ssh/id_rsa.pub* som plats för din SSH-offentliga nyckel.

> [!NOTE]
> Som standard är användarnamnet för AKS-noderna *azureuser*.

När du har lagt till den offentliga SSH-nyckeln i skalningsuppsättningen kan du SSH till en virtuell nod-dator i den skalningsuppsättningen med hjälp av dess IP-adress. Visa de privata IP-adresserna för AKS-klusternoderna med [kommandot kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

I följande exempelutdata visas interna IP-adresser för alla noder i klustret, inklusive en Windows Server-nod.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrera den interna IP-adressen för den nod som du vill felsöka.

Följ stegen i [Skapa SSH-anslutningen](#create-the-ssh-connection)för att komma åt noden med SSH.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurera setbaserade AKS-kluster för virtuell datortillgänglighet för SSH-åtkomst

Om du vill konfigurera ditt setbaserade AKS-kluster för virtuell dator för SSH-åtkomst letar du reda på namnet på klustrets Linux-nod och lägger till den offentliga SSH-nyckeln till den noden.

Använd kommandot [az aks visa][az-aks-show] för att hämta resursgruppnamnet för AKS-klustret och sedan kommandot [az vm-lista][az-vm-list] för att lista namnet på klustrets Linux-nod.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

I exemplet ovan tilldelas namnet på klusterresursgruppen för *myAKSCluster* i *myResourceGroup* till *CLUSTER_RESOURCE_GROUP*. I exemplet används sedan *CLUSTER_RESOURCE_GROUP* för att lista namnet på den virtuella datorn. Exempelutdata visar namnet på den virtuella datorn:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Om du vill lägga till SSH-nycklar i noden använder du kommandot [az vm user update.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

I exemplet ovan används *variabeln CLUSTER_RESOURCE_GROUP* och namnet på den virtuella noden från tidigare kommandon. I exemplet ovan används också *~/.ssh/id_rsa.pub* som plats för din SSH-offentliga nyckel. Du kan också använda innehållet i den offentliga SSH-nyckeln i stället för att ange en sökväg.

> [!NOTE]
> Som standard är användarnamnet för AKS-noderna *azureuser*.

När du har lagt till den offentliga SSH-nyckeln på den virtuella noden kan du SSH i den virtuella datorn med hjälp av dess IP-adress. Visa den privata IP-adressen för en AKS-klusternod med kommandot [az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

I exemplet ovan används *variabeln CLUSTER_RESOURCE_GROUP* i föregående kommandon. Följande exempelutdata visar AKS-nodernas privata IP-adresser:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Skapa SSH-anslutningen

Om du vill skapa en SSH-anslutning till en AKS-nod kör du en hjälpenhet i AKS-klustret. Den här hjälpenheten ger dig SSH-åtkomst till klustret och sedan ytterligare SSH-nodåtkomst. Så här skapar och använder du den här hjälpenheten:

1. Kör `debian` en behållaravbildning och bifoga en terminalsession till den. Den här behållaren kan användas för att skapa en SSH-session med valfri nod i AKS-klustret:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Om du använder Windows Server-noder (för närvarande i förhandsversion i AKS) lägger du till en nodväljare i kommandot för att schemalägga Debianbehållaren på en Linux-nod:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. När terminalsessionen är ansluten till behållaren `apt-get`installerar du en SSH-klient med:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Öppna ett nytt terminalfönster, som inte är anslutet till din behållare, kopiera din privata SSH-nyckel till hjälpenheten. Den här privata nyckeln används för att skapa SSH till AKS-noden. 

   Om det behövs, ändra *~/.ssh/id_rsa* till platsen för din privata SSH-nyckel:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Återgå till terminalsessionen till behållaren, uppdatera behörigheterna för den kopierade `id_rsa` privata SSH-nyckeln så att den är skrivskyddad för användaren:

    ```console
    chmod 0600 id_rsa
    ```

1. Skapa en SSH-anslutning till AKS-noden. Återigen är standardanvändarnamnet för AKS-noder *azureuser*. Acceptera uppmaningen om att fortsätta med anslutningen när SSH-nyckeln först är betrodd. Du får sedan bash prompt av din AKS-nod:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Ta bort SSH-åtkomst

När du `exit` är klar, `exit` SSH-sessionen och sedan den interaktiva behållarsessionen. När den här behållarsessionen stängs tas podden som används för SSH-åtkomst från AKS-klustret bort.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökningsdata kan du [visa kubeletloggarna][view-kubelet-logs] eller [visa kubernetes-huvudnodloggar][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
