---
title: Ansluta virtuella nätverk med peerkoppling – Azure CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du ansluter virtuella nätverk med peerkoppling, med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0f98f815c0417d1ee8ad5708589b5402a033f83a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989549"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Ansluta virtuella nätverk med peerkoppling med hjälp av Azure CLI

Du kan ansluta virtuella nätverk till varandra med peerkoppling. När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här artikeln kan du se hur du:

* Skapa två virtuella nätverk
* Koppla samman två virtuella nätverk med en peerkoppling
* Distribuera en virtuell dator (VM) till varje virtuellt nätverk
* Kommunicera mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

Du måste skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapats i den här artikeln innan du skapar ett virtuellt nätverk. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork1* med adressprefixet *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Skapa ett virtuellt nätverk med namnet *myVirtualNetwork2* med adressprefixet *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

Peering-sessioner upprättas mellan virtuella nätverks-ID, så måste du först skaffa ID för varje virtuellt nätverk med [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) och lagra det ID: T i en variabel.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Skapa en peer-kopplingen från *myVirtualNetwork1* till *myVirtualNetwork2* med [az network vnet-peering skapa](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Om den `--allow-vnet-access` parametern inte anges, en peering har upprättats, men ingen kommunikation kan flöda genom den.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Utdata som returneras när det föregående kommandot körs och se att den **peeringState** är *initierad*. Dina peering blir kvar i den *initierad* tillstånd förrän du har skapat peer-kopplingen från *myVirtualNetwork2* till *myVirtualNetwork1*. Skapa en peer-kopplingen från *myVirtualNetwork2* till *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Utdata som returneras när det föregående kommandot körs och se att den **peeringState** är *ansluten*. Azure har också ändrat peering-tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering till *ansluten*. Bekräfta att peering-tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering ändras till *ansluten* med [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Resurser i ett virtuellt nätverk inte kan kommunicera med resurser i det virtuella nätverket förrän den **peeringState** för peer-kopplingar i båda virtuella nätverken är *ansluten*. 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVm1* i den *myVirtualNetwork1* virtuellt nätverk. Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Den `--no-wait` alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Skapa en virtuell dator i den *myVirtualNetwork2* virtuellt nätverk.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats visar Azure CLI information liknande följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Den här adressen används för att få åtkomst till den virtuella datorn från internet i ett senare steg.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Använd följande kommando för att skapa en SSH-session med den *myVm2* VM. Ersätt `<publicIpAddress>` med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan är den offentliga IP-adressen *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Pinga den virtuella datorn i *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Du får fyra svar. 

Stäng SSH-sessionen till den *myVm2* VM. 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du ansluter två nätverk i samma Azure-region, med peerkoppling. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region) och i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#cli), samt skapa [nav- och ekernätverksdesigner](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling. Mer information om peerkoppling av virtuella nätverk finns i [Översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peerkopplingar av virtuella nätverk](virtual-network-manage-peering.md).

Du kan [ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkopplade virtuella nätverk. Återanvändbara skript att utföra flera av de funktioner som beskrivs i de virtuella nätverket artiklarna finns [skriptexempel](cli-samples.md).