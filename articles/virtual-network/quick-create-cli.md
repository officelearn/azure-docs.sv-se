---
title: Skapa ett virtuellt nätverk – snabbstart – Azure CLI | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera privat med varandra och med internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2290d2975326c35ec4ec18a77a181a4ad0a205b2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962801"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure CLI

Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera privat med varandra och med internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till en virtuell dator från internet och kommunicerar privat med den andra virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.28 eller senare under den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska innehålla det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* med ett undernät som heter *default*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Alternativet `--no-wait` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg. Följande exempel skapar en virtuell dator med namnet *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn returnerar Azure CLI utdata som liknar följande exempel: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Den här adressen används för att ansluta till den virtuella datorn från internet i nästa steg.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från internet

Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din *myVm2* i kommandot som följer, och ange sedan följande kommando:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Om du vill bekräfta privat kommunikation mellan *myVm2* och *myVm1* anger du följande kommando:

```bash
ping myVm1 -c 4
```

Du får fyra svar från *10.0.0.4*.

Avsluta SSH-sessionen med *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md). 

Som standard tillåter Azure obegränsad privat kommunikation mellan virtuella datorer, men tillåter endast anslutningar till fjärrskrivbord till virtuella Windows-datorer från internet. Om du vill lära dig mer om hur du tillåter eller begränsar olika typer av nätverkskommunikation till och från virtuella datorer fortsätter du till [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
