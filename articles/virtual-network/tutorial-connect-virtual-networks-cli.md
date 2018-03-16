---
title: "Ansluta virtuella nätverk med virtuella nätverk peering - Azure CLI | Microsoft Docs"
description: "Lär dig mer om att ansluta virtuella nätverk med virtuella nätverk peering."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: bbf2e757e2d9ad76c59394ba0138a61fd4029d15
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Ansluta virtuella nätverk med virtuella nätverk peering med hjälp av Azure CLI

Du kan ansluta virtuella nätverk till varandra med virtuella nätverk peering. När virtuella nätverk är peerkopplat kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna som fanns i samma virtuella nätverk. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Ansluta två virtuella nätverk med ett virtuellt nätverk som peering
> * Distribuera en virtuell dator (VM) i varje virtuellt nätverk
> * Kommunikation mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt denna Snabbstart kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

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

## <a name="peer-virtual-networks"></a>Peer-virtuella nätverk

Peerkopplingar upprättas mellan virtuella nätverks-ID, så måste du först hämta ID för varje virtuellt nätverk med [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) och lagrar ID i en variabel.

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

Skapa en peering från *myVirtualNetwork1* till *myVirtualNetwork2* med [az network vnet-peering skapa](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Om den `--allow-vnet-access` parameter har angetts, en peering upprättades, men ingen kommunikation kan flöda genom den.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

I utdatan när körs det föregående kommandot visas som den **peeringState** är *initierade*. Peering förblir i det *initierade* tillstånd förrän du skapar peering från *myVirtualNetwork2* till *myVirtualNetwork1*. Skapa en peering från *myVirtualNetwork2* till *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

I utdatan när körs det föregående kommandot visas som den **peeringState** är *ansluten*. Azure även ändrat peering tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering till *ansluten*. Bekräfta att peering tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering ändras till *ansluten* med [az network vnet-peering visa](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Resurser i ett virtuellt nätverk inte kan kommunicera med resurser i det andra virtuella nätverket förrän den **peeringState** för peerkopplingar i båda virtuella nätverken är *ansluten*. 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVm1* i den *myVirtualNetwork1* virtuellt nätverk. Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Den `--no-wait` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg.

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

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn visar Azure CLI information liknar följande exempel: 

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

Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från Internet i ett senare steg.

## <a name="communicate-between-vms"></a>Kommunikation mellan virtuella datorer

Använd följande kommando för att skapa en SSH-session med den *myVm2* VM. Ersätt `<publicIpAddress>` med offentliga IP-adressen för den virtuella datorn. I föregående exempel är den offentliga IP-adressen är *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Pinga den virtuella datorn i *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Du får fyra svar. 

Stäng SSH-session till den *myVm2* VM. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>Registrera sig för globalt virtuella nätverk peering förhandsversion**

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network) för tillgängliga regioner. To-peer-virtuella nätverk över regioner, måste du först registrera för förhandsversionen av genom att utföra följande steg (inom prenumerationen varje virtuella nätverket som du vill peer):

1. Registrera dig för förhandsversionen genom att ange följande kommandon:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Om du försöker peer-virtuella nätverk i olika regioner innan den **RegistrationState** utdata efter att ange föregående kommando är **registrerad** för båda prenumerationer peering misslyckas .

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du ansluter två nätverk med virtuella nätverk peering. I den här artikeln beskrivs hur du ansluter två nätverk i samma Azure-plats, med virtuella nätverk peering. Du kan också peer-virtuella nätverk i [olika regioner](#register)i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#portal) och du kan skapa [NAV och ekrar nätverk Designer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peering. Innan du peering produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med de [peering översikt](virtual-network-peering-overview.md), [hantera peering](virtual-network-manage-peering.md), och [virtuellt nätverk gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Du kan [ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkoppla virtuella nätverk. Fortsätta att skriptexempel för återanvändbara skript att utföra många av de uppgifter som beskrivs i artiklarna virtuellt nätverk.

> [!div class="nextstepaction"]
> [Skriptexempel för virtuellt nätverk](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
