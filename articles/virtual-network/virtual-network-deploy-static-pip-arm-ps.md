---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – PowerShell | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 929378f0eea66d7f2ad7846e50bab7ab4014c010
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652874"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. En offentlig IP-adress kan du kommunicera till en virtuell dator från internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress, så att adressen aldrig ändras. Läs mer om [Statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ändra en offentlig IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller arbeta med privata IP-adresser i avsnittet [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift tas ut](https://azure.microsoft.com/pricing/details/ip-addresses), och det finns en [gränsen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda din lokala dator, se till att du har den [Azure PowerShell installerad](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell, Välj **prova** i det övre högra hörnet i vilken ruta för kommandot som följer. Cloud Shell loggar du in på Azure.

1. Om du använder Cloud Shell, kan du gå vidare till steg 2. Öppna en kommandosession och inloggning i Azure med `Connect-AzAccount`.
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp i regionen östra USA Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Skapa en virtuell dator med den [New-AzVM](/powershell/module/az.Compute/New-azVM) kommando. Den `-AllocationMethod "Static"` alternativet tilldelar en statisk offentlig IP-adress till den virtuella datorn. I följande exempel skapas en Windows Server-dator med en statisk, grundläggande SKU offentlig IP-adress med namnet *myPublicIpAddress*. När du uppmanas, anger du ett användarnamn och lösenord som ska användas som autentiseringsuppgifter för inloggning för den virtuella datorn:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Om den offentliga IP-adressen måste vara en standard-SKU, måste du [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address), [skapa ett nätverksgränssnitt](virtual-network-network-interface.md#create-a-network-interface), [tilldela offentliga IP-adress till nätverksgränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses), och sedan [skapa en virtuell dator med nätverksgränssnittet](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)i steg. Läs mer om [offentliga IP-adressen SKU: er](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn kommer att läggas till backend-poolen med en offentlig Azure Load Balancer, måste SKU: N för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adressen. Mer information finns i [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Visa offentliga IP-adress och kontrollera att den har skapats som en statisk adress med [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure har tilldelats en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
Ändra inte inställningar för IP-adresser i den virtuella datorns operativsystem. Operativsystemet är inte medveten om Azure offentliga IP-adresser. Även om du kan lägga till privata IP-adressinställningarna för operativsystemet, vi rekommenderar att du inte gör det, såvida inte behövs, och inte förrän efter läsning [lägga till en privat IP-adress till ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) att ta bort resursgruppen och alla resurser den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Mer information om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statiska privata IP-adressen](virtual-network-network-interface-addresses.md#add-ip-addresses) till en Azure-dator
- Läs mer om hur du skapar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorer
