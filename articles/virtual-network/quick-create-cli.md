---
title: Skapa ett Azure-nätverk – Azure CLI | Microsoft Docs
description: Snabbt lära dig skapa ett virtuellt nätverk med Azure CLI. Ett virtuellt nätverk gör det möjligt för Azure-resurser, till exempel virtuella datorer, privat kommunicera med varandra och med internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d07f06a1a70c859544c3b1ceb6146dc11e4d10aa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Skapa ett virtuellt nätverk med Azure CLI

Ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer (VM), privat kommunicera med varandra och med Internet. I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk. När du skapar ett virtuellt nätverk kan distribuera du två virtuella datorer i det virtuella nätverket. Sedan ansluter till en virtuell dator från internet och kommunicera privat med andra VM.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.28 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk, måste du skapa en resursgrupp med det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk för standard heter *myVirtualNetwork* med ett undernät med namnet *standard*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om SSH-nycklar inte redan finns en nyckel standardplatsen, skapar dem i kommandot. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Den `--no-wait` alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg. I följande exempel skapas en virtuell dator med namnet *myVm1*:

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

Det tar några minuter att skapa den virtuella datorn. När du har skapat den virtuella datorn returnerar Azure CLI-utdata liknar följande exempel: 

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

Anteckna den **publicIpAddress**. Den här adressen används för att ansluta till den virtuella datorn från Internet i nästa steg.

## <a name="connect-to-a-vm-from-the-internet"></a>Anslut till en virtuell dator från internet

Ersätt `<publicIpAddress>` med den offentliga IP-adressen för din *myVm2* VM i kommandot sätt, och ange sedan följande kommando:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Privat kommunikation mellan virtuella datorer

Bekräfta privat kommunikation mellan de *myVm2* och *myVm1* virtuella datorer, anger du följande kommando:

```bash
ping myVm1 -c 4
```

Du får fyra svar från *10.0.0.4*.

Avsluta SSH-session med den *myVm2* VM.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får skapat du ett virtuellt standardnätverk och två virtuella datorer. Du ansluten till en virtuell dator från Internet och kommunicerade privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om inställningar för virtuellt nätverk i [hantera ett virtuellt nätverk](manage-virtual-network.md). 

Standard Azure tillåter obegränsad privat kommunikation mellan virtuella datorer, men tillåter endast inkommande SSH-sessioner till Linux virtuella datorer från Internet. Om du vill lära dig mer om att tillåta eller begränsa olika typer av kommunikation till och från virtuella datorer, går du vidare till nästa kurs.

> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](tutorial-filter-network-traffic-cli.md)
