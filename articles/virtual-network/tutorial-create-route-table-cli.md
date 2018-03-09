---
title: "Dirigera nätverkstrafik - Azure CLI | Microsoft Docs"
description: "Lär dig mer om att dirigera nätverkstrafik till en routingtabell som använder Azure CLI."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Dirigera nätverkstrafik till en routingtabell som använder Azure CLI

Azure automatiskt vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan skapa egna flöden om du vill åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbart om du till exempel du vill vidarebefordra trafik mellan undernät via en brandvägg. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en routingtabell
> * Skapa en väg
> * Associera en routingtabell till ett undernät för virtuellt nätverk
> * Testa Routning
> * Felsöka routning

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt denna Snabbstart kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Skapa en routingtabell

Azure vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Läs mer om Azures standardvägar i [systemvägar](virtual-networks-udr-overview.md). Om du vill åsidosätta standardvärdet för Azures routning, skapa en routingtabell som innehåller vägar och associera routningstabellen till ett undernät för virtuellt nätverk.

Innan du kan skapa en routingtabell, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create) för alla resurser som har skapats i den här artikeln. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Skapa en routingtabell med [az nätverket routningstabellen skapa](/cli/azure/network/route#az_network_route_table_create). I följande exempel skapas en routingtabell som heter *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Skapa en väg

En routningstabell innehåller noll eller flera vägar. Skapa en väg i routningstabellen med [az nätverksväg routningstabellen skapa](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

Vägen kommer att dirigera all trafik till adressprefixet 10.0.1.0/24 via en virtuell nätverksenhet med IP-adressen 10.0.2.4. Virtuell nätverksenhet och undernät med det angivna adressprefixet skapas i senare steg. Vägen åsidosätter Azures standard routning, som dirigerar trafik mellan undernät direkt. Varje väg anger nästa hopptyp. Nästa hopptyp instruerar Azure så att dirigera trafiken. I det här exemplet nästa hopptyp är *VirtualAppliance*. Läs mer om alla tillgängliga nästa hopptyper i Azure och när de ska användas i [typer för nästa hopp](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routingtabell till ett undernät

Innan du kan koppla en routingtabell till ett undernät som du behöver skapa ett virtuellt nätverk och undernät. Skapa ett virtuellt nätverk med ett undernät med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Skapa två ytterligare undernät med [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Du kan associera en routingtabell till noll eller flera undernät. Ett undernät kan ha noll eller en vägtabell associerad till den. Utgående trafik från ett undernät vidarebefordras baserat på Azures standardvägar och eventuella anpassade vägar som du har lagt till en routingtabell som du kopplar till ett undernät. Koppla den *myRouteTablePublic* routningstabellen till den *offentliga* undernät med [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Innan du distribuerar routningstabeller för produktion, rekommenderas att du noggrant bekanta dig med [routning i Azure](virtual-networks-udr-overview.md) och [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testa Routning

Om du vill testa routning, ska du skapa en virtuell dator som fungerar som virtuell nätverksenhet att flödet som du skapade i föregående steg vägar via. När du har skapat virtuell nätverksenhet du ska distribuera en virtuell dator i den *offentliga* och *privata* undernät. Du kommer sedan att dirigera trafik från den *offentliga* undernät så att den *privata* undernätet via virtuell nätverksenhet.

### <a name="create-a-network-virtual-appliance"></a>Skapa en virtuell nätverksenhet

I föregående steg skapas en väg som angetts av en virtuell nätverksenhet som nexthop-typen. En virtuell dator som kör ett nätverksprogram kallas ofta för en virtuell nätverksenhet. Den virtuella nätverksenhet som du distribuerar är ofta en förkonfigurerad virtuell dator i produktionsmiljöer. Flera virtuella nätverksinstallationer är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En enkel virtuell dator skapas i den här artikeln. 

Skapa en virtuell nätverksenhet i den *DMZ* undernät med [az vm skapa](/cli/azure/vm#az_vm_create). När du skapar en virtuell dator Azure skapar och tilldelar en offentlig IP-adress till den virtuella datorn som standard. Den `--public-ip-address ""` parametern instruerar Azure inte att skapa och tilldela en offentlig IP-adress till den virtuella datorn eftersom den virtuella datorn inte behöver vara ansluten till från Internet. Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och returnerar utdata om den virtuella datorn. Den virtuella nätverksenhet som du distribuerar är ofta en förkonfigurerad virtuell dator i produktionsmiljöer. Flera virtuella nätverksinstallationer är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Du måste aktivera IP-vidarebefordring för varje Azure [nätverksgränssnittet](virtual-network-network-interface.md) kopplad till en virtuell dator som vidarebefordrar trafik till IP-adresser som inte är tilldelade till nätverksgränssnittet. När du skapade virtuell nätverksenhet i den *DMZ* undernät, Azure skapas automatiskt ett nätverksgränssnitt med namnet *myVmNvaVMNic*, kopplade nätverksgränssnittet till den virtuella datorn, och tilldelade privata IP-adressen *10.0.2.4* till nätverksgränssnittet. Aktivera IP-vidarebefordring för nätverksgränssnitt med [az nätverket nic uppdatering](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

I den virtuella datorn, måste operativsystemet eller ett program som körs på den virtuella datorn också kunna vidarebefordra nätverkstrafik. När du distribuerar en virtuell nätverksenhet i en produktionsmiljö anordningen vanligtvis filtrerar, loggar eller utför vissa andra funktion innan den vidarebefordrar trafik. I den här artikeln dock vidarebefordrar operativsystemet all trafik som tas emot. Aktivera IP-vidarebefordring inom den virtuella datorns operativsystem med [az vm-tillägget set](/cli/azure/vm/extension#az_vm_extension_set), som kör ett kommando som aktiverar IP-vidarebefordring i operativsystemet.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Kommandot kan ta upp till en minut att köra.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafik från den *offentliga* undernät dirigeras till den *privata* undernätet via virtuell nätverksenhet i ett senare steg. 

Skapa en virtuell dator i den *offentliga* undernät med [az vm skapa](/cli/azure/vm#az_vm_create). Den `--no-wait` parametern aktiverar Azure för att köra kommandot i bakgrunden så du kan fortsätta med nästa kommando. Ett lösenord används för att förenkla den här artikeln. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Skapa en virtuell dator i den *privata* undernät.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn visar Azure CLI information liknar följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från Internet i ett senare steg.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Använd följande kommando för att skapa en SSH-session med den *myVmMgmt* virtuella datorn. Ersätt  *<publicIpAddress>*  med offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösenord, ange lösenordet som du valde i [skapa virtuella datorer](#create-virtual-machines).

Använd följande kommando för att installera traceroute på den *myVmMgmt* virtuell dator:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning av nätverkstrafik till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella datorn.

```bash
traceroute myvmweb
```

Svaret liknar följande exempel:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Du kan se trafik vidarebefordras direkt från den *myVmMgmt* virtuella datorn till den *myVmWeb* virtuella datorn. Azures standardvägar, vidarebefordra trafik direkt mellan undernät. 

Använd följande kommando för att SSH till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuell dator:

```bash 
ssh azureuser@myVmWeb
```

Använd följande kommando för att installera traceroute på den *myVmWeb* virtuell dator:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning av nätverkstrafik till den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn.

```bash
traceroute myvmmgmt
```

Svaret liknar följande exempel:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Du kan se att det första hoppet är 10.0.2.4, vilket är den virtuella nätverksenhets privat IP-adress. Ett andra hopp är 10.0.1.4 privata IP-adressen för den *myVmMgmt* virtuella datorn. Vägen som lagts till i den *myRouteTablePublic* routningstabellen och som är associerade med den *offentliga* undernät orsakade Azure för att dirigera trafik via en NVA i stället för direkt till den *privata* undernät.

Stäng SSH-sessioner för både den *myVmWeb* och *myVmMgmt* virtuella datorer.

## <a name="troubleshoot-routing"></a>Felsöka routning

Du har lärt dig i föregående steg gäller standardvägar som du kan alternativt kan du åsidosätta med egna vägar i Azure. Ibland kan trafik inte dirigeras som du tror att. Använd [az nätverk watcher visa-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) att avgöra hur trafik dirigeras mellan två virtuella datorer. Till exempel följande kommando testar routning av nätverkstrafik från den *myVmWeb* (10.0.0.4) virtuella datorn till den *myVmMgmt* (10.0.1.4) virtuell dator:

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
Efter en kort vänta returneras följande utdata:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

Utdata informerar dig om att nästa hopp-IP-adress för trafik från *myVmWeb* till *myVmMgmt* är 10.0.2.4 (den *myVmNva* virtuell dator), att nästa hopptyp är  *VirtualAppliance*, och att routningstabellen som orsakar routning är *myRouteTablePublic*.

Effektiva vägar för varje nätverksgränssnitt är en kombination av Azures standardvägar och alla vägar som du definierar. Se alla vägar som gäller för ett nätverksgränssnitt i en virtuell dator med [az nätverk nic visa-gällande--routningstabellen](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Till exempel för att visa effektiva vägarna för den *MyVmWebVMNic* nätverksgränssnitt i den *myVmWeb* virtuella datorn, anger du följande kommando:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Alla standardvägar och flödet som du lade till i föregående steg, returneras.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en virtuell nätverksenhet som dirigeras trafiken från offentliga undernät till ett privat undernät. Resurser för vissa Azure PaaS-tjänster kan inte distribueras till ett virtuellt nätverk medan du kan distribuera många Azure-resurser inom ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten till resurser av vissa Azure PaaS-tjänster till trafik från ett undernät för virtuellt nätverk men. Gå vidare till nästa kurs att lära dig att begränsa nätverksåtkomsten till Azure PaaS-resurser.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](virtual-network-service-endpoints-configure.md#azure-cli)
