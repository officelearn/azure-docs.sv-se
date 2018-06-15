---
title: Använda interna DNS för namnmatchning för virtuell dator med Azure CLI 2.0 | Microsoft Docs
description: Skapa ett virtuellt nätverk nätverkskort och använda interna DNS för namnmatchning för virtuell dator på Azure med Azure CLI 2.0
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
ms.openlocfilehash: 8df9035cf4a5e62102353c701625526e211b7282
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915335"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Skapa virtuella nätverkskort och använda interna DNS för namnmatchning för virtuell dator på Azure
Den här artikeln visar hur du ställer in statiska interna DNS-namn för Linux virtuella datorer med Azure CLI 2.0 virtuella nätverkskort (vNics) och DNS-etikettnamn. Du kan också utföra dessa steg med [Azure CLI 1.0](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Statisk DNS-namn används för permanenta infrastrukturtjänster som en Jenkins build-server, som används för det här dokumentet eller en Git-server.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snabbkommandon
Om du behöver utföra aktiviteten i följande avsnitt finns information de kommandon som krävs. Mer detaljerad information och kontext för varje steg i resten av dokumentet [startar här](#detailed-walkthrough). Om du vill utföra dessa steg behöver du senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

Krav: Resursgrupp, virtuella nätverk och undernät, Nätverkssäkerhetsgrupp med SSH inkommande.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Skapa ett virtuellt nätverkskort med ett statiskt interna DNS-namn
Skapa vNic med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create). Den `--internal-dns-name` CLI-flaggan är för att ställa in DNS-etikett, vilket ger statisk DNS-namn för det virtuella nätverksgränssnittskortet (vNic). I följande exempel skapas ett virtuellt nätverkskort med namnet `myNic`, ansluter den till den `myVnet` virtuella nätverk och skapar en intern DNS-namnpost kallas `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Distribuera en virtuell dator och ansluta vNic
Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Den `--nics` flaggan ansluter vNic till den virtuella datorn under distributionen till Azure. I följande exempel skapas en virtuell dator med namnet `myVM` med Azure hanterade diskar och bifogar vNic med namnet `myNic` från föregående steg:

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

En fullständig kontinuerlig integrering och kontinuerlig distribution (CiCd) infrastrukturen i Azure kräver vissa servrar vara statiska eller långlivade servrar. Du rekommenderas att Azure tillgångar som den virtuella nätverk och Nätverkssäkerhetsgrupper är statiska och resurser som distribueras sällan kvar längre. När ett virtuellt nätverk har distribuerats, kan den återanvändas av nya distributioner utan någon negativ påverkar i infrastrukturen. Du kan senare lägga till en Git-lagringsplatsen server eller en Jenkins automation-server ger CiCd till det här virtuella nätverket för utvecklings- eller testmiljöer.  

Interna DNS-namn är bara lösas i Azure-nätverk. Eftersom DNS-namn är internt, är de inte matchas till utanför internet, vilket ger ytterligare säkerhet i infrastrukturen.

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar `myResourceGroup`, `myNic`, och `myVM`.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp
Börja med att skapa resursgruppen med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i den `westus` plats:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Skapa virtuella nätverk

Nästa steg är att skapa ett virtuellt nätverk för att starta de virtuella datorerna till. Det virtuella nätverket innehåller ett undernät för den här genomgången. Mer information om virtuella Azure-nätverk finns [skapa ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Skapa ett virtuellt nätverk med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet `myVnet` och undernät med namnet `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Skapa Nätverkssäkerhetsgruppen
Azure Nätverkssäkerhetsgrupper är likvärdiga med en brandvägg på nätverksnivå. Läs mer om Nätverkssäkerhetsgrupper [hur du skapar NSG: er i Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Skapa nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp som heter `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Lägg till en inkommande regel för att tillåta SSH
Lägg till en inkommande regel för nätverkssäkerhetsgruppen med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). I följande exempel skapas en regel med namnet `myRuleAllowSSH`:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associera undernätet med Nätverkssäkerhetsgruppen
Om du vill associera undernätet med Nätverkssäkerhetsgruppen använder [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). I följande exempel associerar undernätnamnet `mySubnet` med Nätverkssäkerhetsgruppen med namnet `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Skapa virtuella nätverkskort och statisk DNS-namn
Azure är flexibla, men om du vill använda DNS-namn för VM-namnmatchning, måste du skapa virtuellt nätverkskort (vNics) som innehåller en DNS-etikett. vNics är viktigt eftersom du kan återanvända dem genom att ansluta dem till olika virtuella datorer under hela livscykeln för infrastruktur. Den här metoden används för att hålla vNic som en statisk resurs medan de virtuella datorerna kan vara tillfälligt. Med hjälp av DNS-etiketter på vNic, kommer du att aktivera enkel namnmatchning från andra virtuella datorer i VNet. Med matchas namn kan andra virtuella datorer att ansluta till automatiseringsservern med DNS-namn `Jenkins` eller Git-server som `gitrepo`.  

Skapa vNic med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas ett virtuellt nätverkskort med namnet `myNic`, ansluter den till den `myVnet` virtuellt nätverk med namnet `myVnet`, och skapar en intern DNS-namnpost kallas `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuera den virtuella datorn till den virtuella nätverksinfrastrukturen
Nu har vi ett virtuellt nätverk och undernät, en Nätverkssäkerhetsgrupp fungerar som en brandvägg för att skydda våra undernät genom att blockera all inkommande trafik utom port 22 för SSH- och ett virtuellt nätverkskort. Du kan nu distribuera en virtuell dator i den här befintliga nätverksinfrastruktur.

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet `myVM` med Azure hanterade diskar och bifogar vNic med namnet `myNic` från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Med hjälp av CLI-flaggor för att anropa befintliga resurser, ber vi Azure för att distribuera den virtuella datorn i det befintliga nätverket. Om du vill upprepar när ett VNet och undernät som har distribuerats, kan de förbli statisk eller permanenta resurser i Azure-region.  

## <a name="next-steps"></a>Nästa steg
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM på Azure med hjälp av mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
