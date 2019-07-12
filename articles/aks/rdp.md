---
title: RDP i Azure Kubernetes Service (AKS) noder i Windows Server
description: Lär dig hur du skapar en RDP-anslutning med klustret i Azure Kubernetes Service (AKS) Windows Server-noder för felsökning och underhållsåtgärder.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614568"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Anslut med RDP till Azure Kubernetes Service (AKS) klusternoder i Windows Server för underhåll och felsökning

Under livscykeln för ditt kluster i Azure Kubernetes Service (AKS), kan du behöva komma åt en AKS Windows Server-nod. Den här åtkomsten kan vara för underhåll, Logginsamling eller andra felsökning åtgärder. Du kan komma åt AKS Windows Server-noder med RDP. Om du vill använda SSH för att komma åt AKS Windows Server-noder och du har åtkomst till samma nyckelpar som användes när klustret skapas kan du också följa stegen i [SSH till noder i Azure Kubernetes Service (AKS)][ssh-steps]. Av säkerhetsskäl exponeras inte AKS-noder till internet.

Stöd för Windows Server-noden är för närvarande i förhandsversion i AKS.

Den här artikeln visar hur du skapar en RDP-anslutning med ett AKS-noden med sina privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med en Windows Server-nod. Om du behöver ett AKS-kluster finns i artikeln på [skapar ett AKS-kluster med en Windows-behållare med Azure CLI][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Du också ha Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuera en virtuell dator i samma undernät som klustret

Windows Server-noderna i AKS-klustret har inte den externa IP-adresser. Om du vill göra en RDP-anslutning, kan du distribuera en virtuell dator med en offentligt tillgänglig IP-adress i samma undernät som Windows Server-noder.

I följande exempel skapas en virtuell dator med namnet *myVM* i den *myResourceGroup* resursgrupp.

Hämta först det undernät som används av din pool för Windows Server-nod. För att hämta undernät-id, behöver du namnet på undernätet. För att hämta namnet på undernätet, behöver du namnet på det virtuella nätverket. Hämta virtuella nätverkets namn genom att fråga ditt kluster för sin lista över nätverk. Om du vill fråga klustret, behöver du dess namn. Du kan få alla dessa genom att köra följande i Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Nu när du har SUBNET_ID, kör du följande kommando i samma Azure Cloud Shell-fönstret för att skapa den virtuella datorn:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Följande Exempelutdata visar den virtuella datorn har skapats och visar den offentliga IP-adressen för den virtuella datorn.

```console
13.62.204.18
```

Anteckna den offentliga IP-adressen för den virtuella datorn. Du använder den här adressen i ett senare steg.

## <a name="get-the-node-address"></a>Hämta nodadressen

Om du vill hantera ett Kubernetes-kluster måste du använda [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Installera `kubectl` lokalt, använda den [az aks install-cli][az-aks-install-cli] kommando:
    
```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Lista över den interna IP-adressen med Windows Server-noder med den [kubectl hämta][kubectl-get] kommando:

```console
kubectl get nodes -o wide
```

Följande Exempelutdata visar interna IP-adresserna för alla noder i klustret, inklusive Windows Server-noder.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Anteckna den interna IP-adressen för den Windows Server-noden som du vill felsöka. Du använder den här adressen i ett senare steg.

## <a name="connect-to-the-virtual-machine-and-node"></a>Ansluta till den virtuella datorn och en nod

Ansluta till den offentliga IP-adressen för den virtuella datorn som du skapat tidigare med en RDP-klient som [Microsoft Remote Desktop][rdp-mac].

![Bild för att ansluta till den virtuella datorn med en RDP-klient](media/rdp/vm-rdp.png)

När du har anslutit till den virtuella datorn kan ansluta till den *interna IP-adressen* för Windows Server-nod som du vill felsöka med hjälp av en RDP-klient från den virtuella datorn.

![Bild av att ansluta till Windows Server-noden med hjälp av en RDP-klient](media/rdp/node-rdp.png)

Du är nu ansluten till din Windows Server-nod.

![Bild av kommandofönstret i Windows Server-nod](media/rdp/node-session.png)

Du kan nu köra kommandon som felsökning i den *cmd* fönster. Eftersom Windows Server-noder använder Windows Server Core, är det inte ett fullständigt grafiskt användargränssnitt eller andra GUI-verktyg när du ansluter till en Windows Server-nod via RDP.

## <a name="remove-rdp-access"></a>Ta bort RDP-åtkomst

När du är klar avsluta RDP-anslutning till Windows Server-noden och sedan avslutar du RDP-sessionen till den virtuella datorn. När du har avslutat båda RDP-sessioner, tar du bort den virtuella datorn med den [az vm ta bort][az-vm-delete] kommando:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökning data, kan du [visa Kubernetes-huvudnod loggar][view-master-logs] or [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
