---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - PowerShell | Microsoft-dokument
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 0eb4f86a2484486658171ab4b099794e4ba3e4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043389"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en virtuell dator med en statisk offentlig IP-adress. Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras. Läs mer om [statiska offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Information om hur du ändrar en offentlig IP-adress som tilldelats en befintlig virtuell dator från dynamisk till statisk eller för att arbeta med privata IP-adresser finns [i Lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md). Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses)och det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för antalet offentliga IP-adresser som du kan använda per prenumeration.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan slutföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda den lokala datorn kontrollerar du att du har [Azure PowerShell installerat](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av en kommandoruta som följer. Cloud Shell loggar in dig i Azure.

1. Om du använder Cloud Shell, hoppa till steg 2. Öppna en kommandosession och `Connect-AzAccount`logga in på Azure med .
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp i regionen Östra USA Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Skapa en virtuell dator med kommandot [New-AzVM.](/powershell/module/az.Compute/New-azVM) Alternativet `-AllocationMethod "Static"` tilldelar den virtuella datorn en statisk offentlig IP-adress. I följande exempel skapas en virtuell Windows Server-dator med en statisk, grundläggande SKU-offentlig IP-adress med namnet *myPublicIpAddress*. Ange ett användarnamn och lösenord som ska användas som inloggningsuppgifter för den virtuella datorn när du uppmanas att göra det:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Om den offentliga IP-adressen måste vara en standard-SKU måste du [skapa en offentlig IP-adress,](virtual-network-public-ip-address.md#create-a-public-ip-address) [skapa ett nätverksgränssnitt,](virtual-network-network-interface.md#create-a-network-interface) [tilldela den offentliga IP-adressen till nätverksgränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses)och sedan [skapa en virtuell dator med nätverksgränssnittet](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)i separata steg. Läs mer om [SKU:er för offentlig IP-adress](virtual-network-ip-addresses-overview-arm.md#sku). Om den virtuella datorn läggs till i backend-poolen för en offentlig Azure Load Balancer måste SKU för den virtuella datorns offentliga IP-adress matcha SKU för belastningsutjämnarens offentliga IP-adress. Mer information finns i [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Visa den offentliga IP-adressen som tilldelats och bekräfta att den skapades som en statisk adress med [Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure tilldelade en offentlig IP-adress från adresser som används i den region som du skapade den virtuella datorn i. Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Ändra inte IP-adressinställningarna i den virtuella datorns operativsystem. Operativsystemet känner inte till offentliga Azure-IP-adresser. Även om du kan lägga till privata IP-adressinställningar i operativsystemet rekommenderar vi att du inte gör det om det inte är nödvändigt, och inte förrän efter att ha läst [Lägg till en privat IP-adress i ett operativsystem](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure
- Läs mer om alla [inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator
- Läs mer om hur du skapar virtuella [Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
