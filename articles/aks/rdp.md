---
title: RDP i Azure Kubernetes Service (AKS) noder i Windows Server
description: Lär dig hur du skapar en RDP-anslutning med klustret i Azure Kubernetes Service (AKS) Windows Server-noder för felsökning och underhållsåtgärder.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: twhitney
ms.openlocfilehash: 6b5ebbab717a3db7c9b50549d2762df61c274131
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307354"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Anslut med RDP till Azure Kubernetes Service (AKS) klusternoder i Windows Server för underhåll och felsökning

Under livscykeln för ditt kluster i Azure Kubernetes Service (AKS), kan du behöva komma åt en AKS Windows Server-nod. Den här åtkomsten kan vara för underhåll, Logginsamling eller andra felsökning åtgärder. Du kan komma åt AKS Windows Server-noder med RDP. Om du vill använda SSH för att komma åt AKS Windows Server-noder och du har åtkomst till samma nyckelpar som användes när klustret skapas kan du också följa stegen i [SSH till noder i Azure Kubernetes Service (AKS)] [ssh-steps]. Av säkerhetsskäl exponeras inte AKS-noder till internet.

Stöd för Windows Server-noden är för närvarande i förhandsversion i AKS.

Den här artikeln visar hur du skapar en RDP-anslutning med ett AKS-noden med sina privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med en Windows Server-nod. Om du behöver ett AKS-kluster finns i artikeln på [skapar ett AKS-kluster med en Windows-behållare med Azure CLI][aks-windows-cli]. Du behöver det Windows-administratörsanvändarnamn och lösenord för Windows Server-nod som du vill felsöka. Du behöver också en RDP-klient som [Microsoft Remote Desktop][rdp-mac].

Du också ha Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuera en virtuell dator i samma undernät som klustret

Windows Server-noderna i AKS-klustret har inte den externa IP-adresser. Om du vill göra en RDP-anslutning, kan du distribuera en virtuell dator med en offentligt tillgänglig IP-adress i samma undernät som Windows Server-noder.

I följande exempel skapas en virtuell dator med namnet *myVM* i den *myResourceGroup* resursgrupp. Ersätt *$SUBNET_ID* med ID: T för det undernät som används av din pool för Windows Server-nod.

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

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Lista över den interna IP-adressen med Windows Server-noder med den [kubectl hämta] [ kubectl-get] kommando:

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

När du är klar avsluta RDP-anslutning till Windows Server-noden och sedan avslutar du RDP-sessionen till den virtuella datorn. När du har avslutat båda RDP-sessioner, tar du bort den virtuella datorn med den [az vm ta bort] [ az-vm-delete] kommando:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökning data, kan du [visa Kubernetes-huvudnod loggar] [ view-master-logs] eller [Azure Monitor][azure-monitor-containers].

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
