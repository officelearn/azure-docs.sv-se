---
title: Skapa ett virtuellt nätverk – snabbstart – Azure CLI
titlesuffix: Azure Virtual Network
description: I den här snabbstarten får du lära dig att skapa ett virtuellt nätverk med hjälp av Azure CLI. Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: 1b3dde1b2d638f6dd6c19ce173c7f9f071868d23
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734223"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Snabbstart: Skapa ett virtuellt nätverk med Azure CLI

Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat i det nya virtuella nätverket.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I exemplet skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* med ett undernät som heter *default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm). Om det inte redan finns SSH-nycklar på en standardnyckelplats skapar kommandot dem. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Alternativet `--no-wait` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg. I exemplet skapas en virtuell dator med namnet *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Eftersom du har använt alternativet `--no-wait` i föregående steg, kan du gå vidare och skapa den andra virtuella datorn med namnet *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Utdatameddelande från Azure CLI

Det tar några minuter att skapa de virtuella datorerna. När Azure skapar de virtuella datorerna, returnerar Azure CLI utdata som liknar detta:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna **publicIpAddress**. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

I det här kommandot ersätter vi `<publicIpAddress>` med den offentliga IP-adressen för den virtuella datorn *myVm2*:

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

När de inte behövs längre kan du använda [az group delete](/cli/azure/group) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan de två virtuella datorerna. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md).

Med Azure kan obegränsad privat kommunikation ske mellan virtuella datorer. Som standard tillåter Azure endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om hur du konfigurerar olika typer av nätverkskommunikation för virtuella datorer finns i självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
