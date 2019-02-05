---
title: Använda internt DNS för VM-namnmatchning med Azure CLI | Microsoft Docs
description: Hur du skapar virtuella nätverkskort och använda internt DNS för VM-namnmatchning i Azure med Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 5e893d597c2193676cb350fc80d7baa694ad6fd1
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734130"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Skapa virtuella nätverkskort och använda internt DNS för VM-namnmatchning i Azure

Den här artikeln visar hur du ställer in statiska interna DNS-namn för virtuella Linux-datorer med hjälp av virtuella nätverkskort (Vnic) och DNS-etikettnamn med Azure CLI. Statisk DNS-namn som används för permanent infrastrukturtjänster som en Jenkins build-server, som används för det här dokumentet eller en Git-server.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snabbkommandon
Om du vill åstadkomma snabbt finns i följande avsnitt kommandon som krävs. Mer detaljerad information och kontext för varje steg finns i resten av dokumentet, [börjar visningen här](#detailed-walkthrough). Om du vill utföra dessa steg du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

Krav: Resursgrupp, virtuellt nätverk och undernät, Nätverkssäkerhetsgruppen med SSH inkommande.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Skapa ett virtuellt nätverkskort med en statisk interna DNS-namn
Skapa det virtuella nätverkskortet med [az network nic skapa](/cli/azure/network/nic). Den `--internal-dns-name` CLI-flaggan är för att ställa in DNS-etikett som tillhandahåller statiska DNS-namnet för det virtuella nätverkskortet (vNic). I följande exempel skapas ett virtuellt nätverkskort med namnet `myNic`, ansluter den till den `myVnet` virtuellt nätverk, och skapar en intern DNS-namnpost kallas `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Distribuera en virtuell dator och anslut det virtuella nätverkskortet
Skapa en virtuell dator med [az vm create](/cli/azure/vm). Den `--nics` flaggan ansluter det virtuella nätverkskortet till den virtuella datorn vid distribution till Azure. I följande exempel skapas en virtuell dator med namnet `myVM` med Azure Managed Disks och bifogar det virtuella nätverkskortet med namnet `myNic` från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

En fullständig kontinuerlig integrering och kontinuerlig distribution (CiCd) infrastruktur i Azure kräver vissa servrar vara statiska eller långlivade servrar. Du rekommenderas att Azure-tillgångar som virtuella nätverk och Nätverkssäkerhetsgrupper är statiska och resurser som distribueras sällan kvar längre. När ett virtuellt nätverk har distribuerats, kan den återanvändas av nya distributioner utan någon negativ påverkar i infrastrukturen. Du kan senare lägga till en Git-lagringsplats server eller en Jenkins-server för automation levererar CiCd till det här virtuella nätverket för utvecklings- eller testmiljöer.  

Interna DNS-namn är bara matchas i Azure-nätverk. Eftersom DNS-namn som är interna, är de inte matchas till utanför internet, vilket ger ökad säkerhet i infrastrukturen.

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar `myResourceGroup`, `myNic`, och `myVM`.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp
Börja med att skapa resursgruppen med [az gruppen skapa](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Nästa steg är att skapa ett virtuellt nätverk för att starta de virtuella datorerna till. Det virtuella nätverket innehåller ett undernät för den här genomgången. Mer information om virtuella Azure-nätverk finns i [skapa ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Skapa det virtuella nätverket med [az network vnet skapa](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet `myVnet` och undernät med namnet `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Skapa Nätverkssäkerhetsgruppen
Azure Nätverkssäkerhetsgrupper är likvärdiga med en brandvägg på nätverksnivå. Mer information om Nätverkssäkerhetsgrupper finns i [hur du skapar NSG: er i Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Skapa nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Lägg till en inkommande regel för att tillåta SSH
Lägg till en inkommande regel för nätverkssäkerhetsgruppen med [az network nsg-regel skapar](/cli/azure/network/nsg/rule). I följande exempel skapas en regel med namnet `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Koppla undernätet till Nätverkssäkerhetsgruppen
För att koppla undernätet till Nätverkssäkerhetsgruppen, använda [az network vnet-undernät uppdatering](/cli/azure/network/vnet/subnet). I följande exempel kopplar undernätsnamnet `mySubnet` med Nätverkssäkerhetsgruppen med namnet `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Skapa de virtuella nätverkskort och en statisk DNS-namn
Azure är mycket flexibelt, men för att använda DNS-namn för VM-namnmatchning, måste du skapa virtuella nätverkskort (Vnic) som innehåller en DNS-etikett. virtuella nätverkskort är viktiga eftersom du kan återanvända dem genom att ansluta dem till olika virtuella datorer under hela livscykeln för infrastruktur. Den här metoden ser till att det virtuella nätverkskortet som en statisk resurs medan de virtuella datorerna kan vara tillfälligt. Med hjälp av DNS-etiketter på det virtuella nätverkskortet kommer du att aktivera enkel namnmatchning från andra virtuella datorer i det virtuella nätverket. Med hjälp av matchas namn kan andra virtuella datorer att ansluta till automatiseringsservern genom att DNS-namnet `Jenkins` eller Git-server som `gitrepo`.  

Skapa det virtuella nätverkskortet med [az network nic skapa](/cli/azure/network/nic). I följande exempel skapas ett virtuellt nätverkskort med namnet `myNic`, ansluter den till den `myVnet` virtuellt nätverk med namnet `myVnet`, och skapar en intern DNS-namnpost kallas `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuera den virtuella datorn till den virtuella nätverksinfrastrukturen
Nu har vi ett virtuellt nätverk och undernät, en Nätverkssäkerhetsgrupp fungerar som en brandvägg för att skydda våra undernät genom att blockera all inkommande trafik utom port 22 för SSH- och ett virtuellt nätverkskort. Du kan nu distribuera en virtuell dator i den här befintliga nätverksinfrastrukturen.

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet `myVM` med Azure Managed Disks och bifogar det virtuella nätverkskortet med namnet `myNic` från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Med hjälp av CLI-flaggorna att identifiera befintliga resurser, ber vi Azure för att distribuera den virtuella datorn i det befintliga nätverket. Upprepar när ett virtuellt nätverk och undernät som har distribuerats, kan de fortsätta vara statiska eller permanenta resurser i din Azure-region.  

## <a name="next-steps"></a>Nästa steg
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM på Azure med mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
