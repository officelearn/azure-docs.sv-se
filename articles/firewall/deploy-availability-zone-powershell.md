---
title: Distribuera Azure-brandväggen med Availability Zones med Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med Tillgänglighetszoner med Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704003"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuera en Azure-brandvägg med Tillgänglighetszoner med Azure PowerShell

Azure-brandväggen kan konfigureras vid distribution till sträcker sig över flera Tillgänglighetszoner för ökad tillgänglighet.

Den här funktionen gör det möjligt för följande scenarier:

- Du kan öka tillgängligheten för 99,99% garanterad drifttid. Mer information finns i Azure-brandväggen [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% garanterad drifttid serviceavtal (SLA) erbjuds när två eller fler Tillgänglighetszoner har markerats.
- Du kan även associera Azure-brandväggen till en viss zon för närhet orsaker, med hjälp av det tjänsten standard enligt serviceavtalet 99,95%.

Läs mer om Tillgänglighetszoner i Azure-brandväggen, [vad är Azure-brandvägg?](overview.md)

I följande Azure PowerShell-exempel visas hur du kan distribuera en Azure-brandvägg med Tillgänglighetszoner.

## <a name="create-a-firewall-with-availability-zones"></a>Skapa en brandväggsregel med Tillgänglighetszoner

Det här exemplet skapar en brandvägg i zon 1, 2 och 3.

När den offentliga IP-adressen som standard skapas har ingen specifik zon angetts. Detta skapar en zonredundant IP-adress som standard. Offentliga IP-adresser kan konfigureras i alla zoner eller en enskild zon.

Det är viktigt att veta, eftersom du inte kan ha en brandvägg i zon 1 och en IP-adress i zon 2. Men du kan ha en brandvägg i zon 1 och IP-adressen i alla zoner eller en brandvägg och en IP-adress i samma enskild zon för närhet.

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

- [Självstudier: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
