---
title: Använd intern DNS för VM-namnmatchning med Azure CLI
description: Så här skapar du virtuella nätverkskort och använder intern DNS för VM-namnmatchning på Azure med Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 07a78e4987a844627824ac5034046cf6a393ad8d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757840"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Skapa virtuella nätverkskort och använd intern DNS för VM-namnmatchning på Azure

Den här artikeln visar hur du ställer in statiska interna DNS-namn för virtuella Linux-datorer med virtuella nätverkskort (vNics) och DNS-etikettnamn med Azure CLI. Statiska DNS-namn används för permanenta infrastrukturtjänster som en Jenkins-byggserver, som används för det här dokumentet eller en Git-server.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Snabbkommandon
Om du snabbt behöver utföra uppgiften beskrivs följande avsnitt de kommandon som behövs. Mer detaljerad information och sammanhang för varje steg finns i resten av dokumentet, [med början här](#detailed-walkthrough). För att kunna utföra dessa steg behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

Förkrav: Resursgrupp, virtuellt nätverk och undernät, Nätverkssäkerhetsgrupp med SSH-inkommande.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Skapa ett virtuellt nätverkskort med ett statiskt internt DNS-namn
Skapa vNic med [az nätverk nic skapa](/cli/azure/network/nic). `--internal-dns-name` CLI-flaggan är till för att ange DNS-etiketten, som ger det statiska DNS-namnet för det virtuella nätverkskortet (vNic). I följande exempel skapas `myNic`en vNic med `myVnet` namnet , ansluter den till det `jenkins`virtuella nätverket och en intern DNS-namnpost med namnet :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Distribuera en virtuell dator och ansluta vNic
Skapa en virtuell dator med [az vm create](/cli/azure/vm). Flaggan `--nics` ansluter vNic till den virtuella datorn under distributionen till Azure. I följande exempel skapas `myVM` en virtuell dator med namnet Azure `myNic` Managed Disks och vNic namnges från föregående steg:

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

En fullständig kontinuerlig integration och kontinuerlig distribution (CiCd) infrastruktur på Azure kräver vissa servrar att vara statiska eller långlivade servrar. Vi rekommenderar att Azure-resurser som virtuella nätverk och nätverkssäkerhetsgrupper är statiska och långlivade resurser som sällan distribueras. När ett virtuellt nätverk har distribuerats kan det återanvändas av nya distributioner utan några negativa effekter på infrastrukturen. Du kan senare lägga till en Git-databasserver eller en Jenkins-automatiseringsserver som levererar CiCd till det här virtuella nätverket för utvecklings- eller testmiljöer.  

Interna DNS-namn kan bara lösas i ett virtuellt Azure-nätverk. Eftersom DNS-namnen är interna kan de inte lösas upp till externt internet, vilket ger ytterligare säkerhet till infrastrukturen.

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempel på `myResourceGroup`parameternamn är , `myNic`och `myVM`.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp
Skapa först resursgruppen med [az-gruppge](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Nästa steg är att bygga ett virtuellt nätverk för att starta de virtuella datorerna till. Det virtuella nätverket innehåller ett undernät för den här genomgången. Mer information om virtuella Azure-nätverk finns i [Skapa ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Skapa det virtuella nätverket med [az nätverk vnet skapa](/cli/azure/network/vnet). I följande exempel skapas `myVnet` ett virtuellt `mySubnet`nätverk med namnet och undernätet med namnet :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Skapa nätverkssäkerhetsgruppen
Azure Network Security Groups motsvarar en brandvägg på nätverkslagret. Mer information om nätverkssäkerhetsgrupper finns i [Så här skapar du NSG:er i Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Skapa nätverkssäkerhetsgruppen med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en `myNetworkSecurityGroup`nätverkssäkerhetsgrupp med namnet :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Lägga till en inkommande regel för att tillåta SSH
Lägg till en inkommande regel för nätverkssäkerhetsgruppen med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule). I följande exempel skapas `myRuleAllowSSH`en regel med namnet :

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associera undernätet med nätverkssäkerhetsgruppen
Om du vill associera undernätet med nätverkssäkerhetsgruppen använder du [az network vnet subnet update](/cli/azure/network/vnet/subnet). I följande exempel associeras `mySubnet` undernätsnamnet med `myNetworkSecurityGroup`nätverkssäkerhetsgruppen:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Skapa det virtuella nätverkskortet och statiska DNS-namn
Azure är mycket flexibelt, men för att använda DNS-namn för VM-namnmatchning måste du skapa virtuella nätverkskort (vNics) som innehåller en DNS-etikett. vNics är viktiga eftersom du kan återanvända dem genom att ansluta dem till olika virtuella datorer under infrastrukturlivscykeln. Den här metoden behåller vNic som en statisk resurs medan de virtuella datorerna kan vara tillfälliga. Genom att använda DNS-märkning på vNic kan vi aktivera enkel namnmatchning från andra virtuella datorer i det virtuella nätverket. Med hjälp av lösbara namn kan andra virtuella datorer `Jenkins` komma åt automatiseringsservern med DNS-namnet eller Git-servern som `gitrepo`.  

Skapa vNic med [az nätverk nic skapa](/cli/azure/network/nic). I följande exempel skapas `myNic`en vNic med `myVnet` namnet `myVnet`, ansluter den till det `jenkins`virtuella nätverket med namnet och en intern DNS-namnpost med namnet :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuera den virtuella datorn i den virtuella nätverksinfrastrukturen
Vi har nu ett virtuellt nätverk och undernät, en network security group som fungerar som en brandvägg för att skydda vårt undernät genom att blockera all inkommande trafik utom port 22 för SSH och en vNic. Du kan nu distribuera en virtuell dator i den befintliga nätverksinfrastrukturen.

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas `myVM` en virtuell dator med namnet Azure `myNic` Managed Disks och vNic namnges från föregående steg:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Genom att använda CLI-flaggorna för att anropa befintliga resurser instruerar vi Azure att distribuera den virtuella datorn i det befintliga nätverket. Om du vill upprepa, när ett virtuellt nätverk och undernät har distribuerats, kan de lämnas som statiska eller permanenta resurser i din Azure-region.  

## <a name="next-steps"></a>Nästa steg
* [Skapa en egen anpassad miljö för en virtuell Linux-dator med hjälp av Azure CLI-kommandon](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator på Azure med mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
