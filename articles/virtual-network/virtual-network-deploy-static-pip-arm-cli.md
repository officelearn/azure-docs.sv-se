---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: eafdbf731ce6ae37c321712d7574ce578e704cc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743062"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure CLI

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. En offentlig IP-adress kan du kommunicera till en virtuell dator från internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress, så att adressen aldrig ändras. Läs mer om [Statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller arbeta med privata IP-adresser i avsnittet [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift tas ut](https://azure.microsoft.com/pricing/details/ip-addresses), och det finns en [gränsen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda din lokala dator, se till att du har den [Azure CLI installerat](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell, Välj **prova** i det övre högra hörnet i vilken ruta för kommandot som följer. Cloud Shell loggar du in på Azure.

1. Om du använder Cloud Shell, kan du gå vidare till steg 2. Öppna en kommandosession och inloggning i Azure med `az login`.
2. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp i regionen östra USA Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az-vm-create). Den `--public-ip-address-allocation=static` alternativet tilldelar en statisk offentlig IP-adress till den virtuella datorn. I följande exempel skapas en virtuell Ubuntu-dator med en statisk, grundläggande SKU offentlig IP-adress med namnet *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Om den offentliga IP-adressen måste vara en standard-SKU, lägger du till `--public-ip-sku Standard` i föregående kommandot. Läs mer om [offentliga IP-adressen SKU: er](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn kommer att läggas till backend-poolen med en offentlig Azure Load Balancer, måste SKU: N för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adressen. Mer information finns i [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Visa offentliga IP-adress och kontrollera att den har skapats som en statisk, grundläggande SKU adress med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure har tilldelats en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte inställningar för IP-adresser i den virtuella datorns operativsystem. Operativsystemet är inte medveten om Azure offentliga IP-adresser. Även om du kan lägga till privata IP-adressinställningarna för operativsystemet, vi rekommenderar att du inte gör det, såvida inte behövs, och inte förrän efter läsning [lägga till en privat IP-adress till ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla relaterade resurser den innehåller:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Mer information om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statiska privata IP-adressen](virtual-network-network-interface-addresses.md#add-ip-addresses) till en Azure-dator
- Läs mer om hur du skapar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorer
