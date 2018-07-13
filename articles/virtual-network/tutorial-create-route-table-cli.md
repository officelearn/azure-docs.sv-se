---
title: Dirigera nätverkstrafik – Azure CLI | Microsoft Docs
description: I den här artikeln lär du dig hur du dirigerar nätverkstrafik med en routningstabell med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: eb4a28b5a57d7e301e800cd4ad87c56b7c5df6d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701843"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Dirigera nätverkstrafik med en routningstabell med hjälp av Azure CLI

Azure dirigerar automatiskt trafik mellan alla undernät inom ett virtuella nätverk som standard. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här artikeln kan du se hur du:

* Skapa en routningstabell
* Skapa en väg
* Skapa ett virtuellt nätverk med flera undernät
* Associera en routningstabell till ett undernät
* Skapa en NVA som dirigerar trafik
* Distribuera virtuella datorer till olika undernät
* Dirigera trafik från ett undernät till ett annat via en NVA

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Skapa en routningstabell

Innan du kan skapa en routningstabell, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create) för alla resurser som skapats i den här artikeln. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Skapa en routningstabell med [az network route-table skapa](/cli/azure/network/route#az_network_route_table_create). I följande exempel skapas en routningstabell med namnet *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Skapa en väg

Skapa en väg i routningstabellen med [az network route-table route skapa](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan associera en routningstabell till ett undernät, måste du skapa ett virtuellt nätverk och undernät. Skapa ett virtuellt nätverk med ett undernät med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Skapa två ytterligare undernät med [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

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

Associera den *myRouteTablePublic* routningstabellen till den *offentliga* undernätet med [az network vnet-undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en nätverksfunktion, som routning, brandvägg eller WAN-optimering.

Skapa en NVA i den *DMZ* undernätet med [az vm skapa](/cli/azure/vm#az_vm_create). När du skapar en virtuell dator, Azure skapar och tilldelar en offentlig IP-adress till den virtuella datorn som standard. Den `--public-ip-address ""` parametern instruerar Azure inte att skapa och tilldela en offentlig IP-adress till den virtuella datorn eftersom den virtuella datorn inte behöver anslutas till från internet. Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

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

För att ett nätverksgränssnitt ska kunna vidarebefordra nätverkstrafik som skickas till det som inte är avsett för den egna IP-adressen måste IP-vidarebefordran vara aktiverat för nätverksgränssnittet. Aktivera IP-vidarebefordring för nätverksgränssnittet med [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

I den virtuella datorn måste operativsystemet, eller ett program som körs i den virtuella datorn, kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordring i operativsystemet för den virtuella datorn med [az vm-tilläggsuppsättningen](/cli/azure/vm/extension#az_vm_extension_set):

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

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafiken från den *offentliga* undernätet dirigeras till den *privata* undernätet via NVA i ett senare steg. 

Skapa en virtuell dator i den *offentliga* undernätet med [az vm skapa](/cli/azure/vm#az_vm_create). Den `--no-wait` parameter gör det möjligt för Azure för att köra kommandot i bakgrunden, så att du kan fortsätta med nästa kommando. För att förenkla den här artikeln används ett lösenord. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

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

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information liknande följande exempel: 

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
Anteckna **publicIpAddress**. Den här adressen används för att få åtkomst till den virtuella datorn från internet i ett senare steg.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

Använd följande kommando för att skapa en SSH-session med den *myVmPrivate* VM. Ersätt *<publicIpAddress>* med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du tillfrågas om ett lösenord, anger du lösenordet som du valde i [skapa virtuella datorer](#create-virtual-machines).

Använd följande kommando för att installera vägspårning på den *myVmPrivate* VM:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routningen för trafik till den *myVmPublic* virtuell dator från den *myVmPrivate* VM.

```bash
traceroute myVmPublic
```

Svaret liknar följande exempel:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Du ser att trafik vidarebefordras direkt från *myVmPrivate* till the *myVmPublic*. Azures standardvägar dirigerar, dirigera trafik direkt mellan undernät. 

Använd följande kommando för att SSH till den *myVmPublic* virtuell dator från den *myVmPrivate* VM:

```bash 
ssh azureuser@myVmPublic
```

Använd följande kommando för att installera vägspårning på den *myVmPublic* VM:

```bash 
sudo apt-get install traceroute
```

Använd följande kommando för att testa routningen för trafik till den *myVmPrivate* virtuell dator från den *myVmPublic* VM.

```bash
traceroute myVmPrivate
```

Svaret liknar följande exempel:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Du kan se att det första hoppet är 10.0.2.4, som är NVA-enhetens privata IP-adress. Det andra hoppet är is 10.0.1.4, som är den privata IP-adressen för den virtuella datorn *myVmPrivate*. Vägen som har lagts till i routningstabellen *myRouteTablePublic* och associerats till det *offentliga* undernätet gjorde så att Azure dirigerade trafiken via NVA istället för direkt till det *privata* undernätet.

Stäng SSH-sessionerna till både den *myVmPublic* och *myVmPrivate* virtuella datorer.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en routningstabell och associerat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Distribuera en mängd olika förkonfigurerade NVA-enheter som utför nätverksfunktioner som brandvägg och WAN-optimering från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men resurser för vissa Azure PaaS-tjänster går inte att distribuera till ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten för resurserna i vissa Azure PaaS-tjänster till trafik enbart från ett undernät för ett virtuell dator. Läs hur genom att läsa [begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources-cli.md).
