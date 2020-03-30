---
title: Distribuera Azure-brandväggen med tillgänglighetszoner med PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med tillgänglighetszoner med Hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195913"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuera en Azure Firewall med Tillgänglighetszoner med Azure PowerShell

Azure-brandväggen kan konfigureras under distributionen för att sträcka sig över flera tillgänglighetszoner för ökad tillgänglighet.

Med den här funktionen kan du aktivera följande scenarier:

- Du kan öka tillgängligheten till 99,99% drifttid. Mer information finns i [Service Level Agreement (SLA) (Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)Firewall Service Level Agreement). Serviceavtalet på 99,99 % drifttid erbjuds när två eller flera tillgänglighetszoner väljs.
- Du kan också associera Azure-brandväggen till en viss zon bara av närhetsskäl med hjälp av servicestandarden 99,95 % SLA.

Mer information om tillgänglighetszoner för Azure-brandväggar finns i [Vad är Azure-brandväggen?](overview.md)

Följande Azure PowerShell-exempel visar hur du kan distribuera en Azure-brandvägg med tillgänglighetszoner.

## <a name="create-a-firewall-with-availability-zones"></a>Skapa en brandvägg med tillgänglighetszoner

I det här exemplet skapas en brandvägg i zonerna 1, 2 och 3.

När standard-offentlig IP-adress skapas anges ingen specifik zon. Detta skapar en zonundant IP-adress som standard. Vanliga offentliga IP-adresser kan konfigureras antingen i alla zoner eller i en enda zon.

Det är viktigt att veta, eftersom du inte kan ha en brandvägg i zon 1 och en IP-adress i zon 2. Men du kan ha en brandvägg i zon 1 och IP-adress i alla zoner, eller en brandvägg och en IP-adress i samma enda zon för närhetsändamål.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
