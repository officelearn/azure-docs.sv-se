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
ms.date: 03/13/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 67bfc8ee677a14735174e9501fa5e10a69bd1ec7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Dirigera nätverkstrafik till en routingtabell som använder Azure CLI

Azure automatiskt vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan skapa egna flöden om du vill åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbart om du exempelvis vill vidarebefordra trafik mellan undernät via en virtuell nätverksenhet (NVA). I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en routingtabell
> * Skapa en väg
> * Skapa ett virtuellt nätverk med flera undernät
> * Associera en routingtabell till ett undernät
> * Skapa en NVA som dirigerar trafik
> * Distribuera virtuella datorer (VM) i olika undernät
> * Vidarebefordra trafik från ett undernät till en annan genom en NVA

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt denna Snabbstart kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Skapa en routingtabell

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

Skapa en väg i routningstabellen med [az nätverksväg routningstabellen skapa](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

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

Koppla den *myRouteTablePublic* routningstabellen till den *offentliga* undernät med [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en funktion i nätverket, till exempel routning, firewalling eller WAN-optimering.

Skapa en NVA i den *DMZ* undernät med [az vm skapa](/cli/azure/vm#az_vm_create). När du skapar en virtuell dator, Azure skapar och tilldelar en offentlig IP-adress till den virtuella datorn som standard. Den `--public-ip-address ""` parametern instruerar Azure inte att skapa och tilldela en offentlig IP-adress till den virtuella datorn eftersom den virtuella datorn inte behöver vara ansluten till från Internet. Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

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

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och returnerar utdata om den virtuella datorn. 

För ett nätverksgränssnitt för att kunna vidarebefordra trafik skickas till den, som inte är avsett för sin egen IP-adressen, måste IP-vidarebefordran aktiveras för nätverksgränssnittet. Aktivera IP-vidarebefordring för nätverksgränssnitt med [az nätverket nic uppdatering](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

I den virtuella datorn, måste operativsystemet eller ett program som körs på den virtuella datorn också kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordring i operativsystemet för den virtuella datorn med [az vm-tillägget set](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Kommandot kan ta upp till en minut att köra.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafik från den *offentliga* undernät dirigeras till den *privata* undernätet via en NVA i ett senare steg. 

Skapa en virtuell dator i den *offentliga* undernät med [az vm skapa](/cli/azure/vm#az_vm_create). Den `--no-wait` parametern aktiverar Azure för att köra kommandot i bakgrunden så du kan fortsätta med nästa kommando. Ett lösenord används för att förenkla den här artikeln. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
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
  --name myVmPrivate \
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
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från Internet i ett senare steg.

## <a name="route-traffic-through-an-nva"></a>Vidarebefordra trafik via en NVA

Använd följande kommando för att skapa en SSH-session med den *myVmPrivate* VM. Ersätt  *<publicIpAddress>*  med offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösenord, ange lösenordet som du valde i [skapa virtuella datorer](#create-virtual-machines).

Använd följande kommando för att installera traceroute på den *myVmPrivate* VM:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning av nätverkstrafik till den *myVmPublic* virtuell dator från den *myVmPrivate* VM.

```bash
traceroute myVmPublic
```

Svaret liknar följande exempel:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Du kan se trafik vidarebefordras direkt från den *myVmPrivate* så att den *myVmPublic* VM. Azures standardvägar, vidarebefordra trafik direkt mellan undernät. 

Använd följande kommando för att SSH till den *myVmPublic* virtuell dator från den *myVmPrivate* VM:

```bash 
ssh azureuser@myVmPublic
```

Använd följande kommando för att installera traceroute på den *myVmPublic* VM:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routning av nätverkstrafik till den *myVmPrivate* virtuell dator från den *myVmPublic* VM.

```bash
traceroute myVmPrivate
```

Svaret liknar följande exempel:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Du kan se att det första hoppet är 10.0.2.4, vilket är en NVA privat IP-adress. Ett andra hopp är 10.0.1.4 privata IP-adressen för den *myVmPrivate* VM. Vägen som lagts till i den *myRouteTablePublic* routningstabellen och som är associerade med den *offentliga* undernät orsakade Azure för att dirigera trafik via en NVA i stället för direkt till den *privata* undernät.

Stäng SSH-sessioner för både den *myVmPublic* och *myVmPrivate* virtuella datorer.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en enkel NVA som dirigeras trafiken från offentliga undernät till ett privat undernät. Distribuera en mängd olika förkonfigurerade NVAs som utför nätverks-funktioner, till exempel brandvägg och WAN-optimering från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Innan du distribuerar routningstabeller för produktion, rekommenderas att du noggrant bekanta dig med [routning i Azure](virtual-networks-udr-overview.md), [hantera vägtabeller](manage-route-table.md), och [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Resurser för vissa Azure PaaS-tjänster kan inte distribueras till ett virtuellt nätverk medan du kan distribuera många Azure-resurser inom ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten till resurser av vissa Azure PaaS-tjänster till trafik från ett undernät för virtuellt nätverk men. Gå vidare till nästa kurs att lära dig att begränsa nätverksåtkomsten till Azure PaaS-resurser.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](virtual-network-service-endpoints-configure.md#azure-cli)
