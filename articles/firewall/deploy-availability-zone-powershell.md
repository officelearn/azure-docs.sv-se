---
title: Distribuera Azure-brandväggen med Availability Zones med Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med Tillgänglighetszoner med Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276917"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuera en Azure-brandvägg med Tillgänglighetszoner med Azure PowerShell

> [!IMPORTANT]
> Azure-brandväggen med Availability Zones är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

- [Självstudie: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
