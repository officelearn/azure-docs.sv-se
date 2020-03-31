---
title: 'Azure ExpressRoute: Migrera klassiska virtuella nätverk till Resurshanteraren'
description: På den här sidan beskrivs hur du migrerar ExpressRoute-associerade virtuella nätverk till Resource Manager när du har flyttat kretsen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061328"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrera ExpressRoute-associerade virtuella nätverk från klassiska till Resource Manager

I den här artikeln beskrivs hur du migrerar ExpressRoute-associerade virtuella nätverk från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen efter att du har flyttat expressroutekretsen. 

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Kontrollera att du har de senaste versionerna av Azure PowerShell-modulerna. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Information om hur du installerar PowerShell Service Management-modulen (som behövs för den klassiska distributionsmodellen) finns i [Installera Azure PowerShell Service Management Module](/powershell/azure/servicemanagement/install-azure-ps).
* Kontrollera att du har granskat [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.
* Granska informationen som finns under [Flytta en ExpressRoute-krets från klassisk till Resurshanteraren](expressroute-move.md). Se till att du till fullo förstår gränserna och begränsningarna.
* Kontrollera att kretsen är i full drift i den klassiska distributionsmodellen.
* Kontrollera att du har en resursgrupp som har skapats i resurshanterarens distributionsmodell.
* Läs följande dokumentation för resursmigrering:

    * [Migrering av IaaS-resurser som stöds av plattformen från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Vanliga frågor: Migrering med plattformsstöd för IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Granska de vanligaste migreringsfelen och mildrande åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenarier som stöds och som inte stöds

* En ExpressRoute-krets kan flyttas från den klassiska till Resource Manager-miljön utan avbrott. Du kan flytta alla ExpressRoute-kretsar från klassikern till Resource Manager-miljön utan driftstopp. Följ instruktionerna för [att flytta ExpressRoute-kretsar från den klassiska till Resurshanterarens distributionsmodell med PowerShell](expressroute-howto-move-arm.md). Detta är en förutsättning för att flytta resurser som är anslutna till det virtuella nätverket.
* Virtuella nätverk, gateways och associerade distributioner i det virtuella nätverket som är anslutna till en ExpressRoute-krets i samma prenumeration kan migreras till Resource Manager-miljön utan avbrott. Du kan följa de steg som beskrivs senare för att migrera resurser som virtuella nätverk, gateways och virtuella datorer som distribueras inom det virtuella nätverket. Du måste se till att de virtuella nätverken är korrekt konfigurerade innan de migreras. 
* Virtuella nätverk, gateways och associerade distributioner inom det virtuella nätverket som inte finns i samma prenumeration som ExpressRoute-kretsen kräver vissa driftstopp för att slutföra migreringen. I det sista avsnittet i dokumentet beskrivs de steg som ska följas för att migrera resurser.
* Det går inte att migrera ett virtuellt nätverk med både ExpressRoute Gateway och VPN Gateway.
* ExpressRoute-övergång till korsabonnemangsrutning stöds inte. Mer information finns i [Support för Microsoft.Network move](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Flytta en ExpressRoute-krets från klassisk till Resurshanteraren
Du måste flytta en ExpressRoute-krets från den klassiska till Resource Manager-miljön innan du försöker migrera resurser som är kopplade till ExpressRoute-kretsen. Om du vill utföra den här uppgiften finns i följande artiklar:

* Granska informationen som finns under [Flytta en ExpressRoute-krets från klassisk till Resurshanteraren](expressroute-move.md).
* [Flytta en krets från klassisk till Resource Manager med Azure PowerShell](expressroute-howto-move-arm.md).
* Använd Azure Service Management-portalen. Du kan följa arbetsflödet för att [skapa en ny ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och välja importalternativet. 

Den här åtgärden innebär inte driftstopp. Du kan fortsätta att överföra data mellan din lokal och Microsoft medan migreringen pågår.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrera virtuella nätverk, gateways och associerade distributioner

Vilka steg du följer för att migrera beror på om dina resurser finns i samma prenumeration, olika prenumerationer eller båda.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrera virtuella nätverk, gateways och associerade distributioner i samma prenumeration som ExpressRoute-kretsen
I det här avsnittet beskrivs de steg som ska följas för att migrera ett virtuellt nätverk, en gateway och associerade distributioner i samma prenumeration som ExpressRoute-kretsen. Inga driftstopp är associerade med den här migreringen. Du kan fortsätta att använda alla resurser genom migreringsprocessen. Hanteringsplanet är låst medan migreringen pågår. 

1. Kontrollera att ExpressRoute-kretsen har flyttats från klassikern till Resource Manager-miljön.
2. Kontrollera att det virtuella nätverket har förberetts på rätt sätt för migreringen.
3. Registrera din prenumeration för resursmigrering. Om du vill registrera prenumerationen för resursmigrering använder du följande PowerShell-kodavsnitt:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Validera, förbereda och migrera. Om du vill flytta det virtuella nätverket använder du följande PowerShell-kodavsnitt:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Du kan också avbryta migreringen genom att köra följande PowerShell-cmdlet:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Nästa steg
* [Migrering av IaaS-resurser som stöds av plattformen från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Vanliga frågor: Migrering med plattformsstöd för IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Granska de vanligaste migreringsfelen och mildrande åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
