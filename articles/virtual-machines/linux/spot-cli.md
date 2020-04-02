---
title: Använda CLI för att distribuera virtuella Azure Spot-datorer
description: Lär dig hur du använder CLI för att distribuera virtuella Azure Spot-datorer för att spara kostnader.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5b6a07bfbcf56f3ca78fa4991e7741a3d44c25b9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544359"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Distribuera spot-datorer med Azure CLI

Med hjälp av [virtuella Azure Spot-datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spot-datorer. Därför är spot-virtuella datorer bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Priserna för spot-virtuella datorer varierar, baserat på region och SKU. Mer information finns i VM-priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Du har möjlighet att ställa in ett maxpris som du är villig att betala, per timme, för den virtuella datorn. Maxpriset för en spot-VM kan ställas in i US-dollar (USD), med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du ställer in `-1`maxpriset så kommer den virtuella datorn inte att vräkas baserat på priset. Priset för den virtuella datorn blir det aktuella priset för Spot eller priset för en vanlig virtuell dator, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig. Mer information om hur du anger maxpriset finns i [Spot-virtuella datorer - Prissättning](spot-vms.md#pricing).

Processen för att skapa en virtuell dator med Spot med Azure CLI är samma som beskrivs i [snabbstartsartikeln](/azure/virtual-machines/linux/quick-create-cli). Lägg bara till parametern "--priority Spot" `-1`och ge ett maxpris eller .


## <a name="install-azure-cli"></a>Installera Azure CLI

Om du vill skapa spot-datorer måste du köra Azure CLI version 2.0.74 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

Logga in på Azure med [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Skapa en plats-VM

Det här exemplet visar hur du distribuerar en virtuell linuxplats som inte kommer att vräkas baserat på priset. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

När den virtuella datorn har skapats kan du fråga för att se det högsta faktureringspriset för alla virtuella datorer i resursgruppen.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Nästa steg**

Du kan också skapa en spot-vm med [Azure PowerShell](../windows/spot-powershell.md) eller en [mall](spot-template.md).

Om du stöter på ett fel läser du [Felkoder](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
