---
title: 'Migrera ExpressRoute associerade virtuella nätverk från classic till Resource Manager: Azure: PowerShell | Microsoft Docs'
description: Den här sidan beskrivs hur du migrerar tillhörande virtuella nätverk till Resource Manager när du har flyttat kretsen.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850828"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrera ExpressRoute associerade virtuella nätverk från classic till Resource Manager

Den här artikeln förklarar hur du migrerar Azure ExpressRoute associerade virtuella nätverk från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen när du har flyttat ExpressRoute-kretsen. 


## <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har den senaste versionen av Azure PowerShell-moduler. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Kontrollera att du har granskat den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Granska informationen som ges enligt [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Kontrollera att du förstår de gränser och begränsningar.
* Kontrollera att kretsen är fullt fungerande i den klassiska distributionsmodellen.
* Se till att du har en resursgrupp som skapades i Resource Manager-distributionsmodellen.
* Granska följande resursmigrering dokumentation:

    * [Plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Vanliga frågor och svar: Plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Granska de vanligaste migreringsfel och åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenarier som stöds och som inte stöds

* En ExpressRoute-krets kan flyttas från klassiskt Resource Manager-miljön utan driftavbrott. Du kan flytta ExpressRoute-krets från klassiskt Resource Manager-miljön utan avbrott. Följ instruktionerna i [flyttar ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen med hjälp av PowerShell](expressroute-howto-move-arm.md). Det här är en förutsättning för att flytta resurser som är anslutna till det virtuella nätverket.
* Virtuella nätverk, gatewayenheter och associerade distributioner i det virtuella nätverket som är kopplade till en ExpressRoute-krets i samma prenumeration kan migreras till Resource Manager-miljö utan driftavbrott. Du kan följa stegen som beskrivs senare om du vill migrera resurser, till exempel virtuella nätverk, gateways och virtuella datorer distribueras i det virtuella nätverket. Du måste se till att de virtuella nätverken är korrekt konfigurerade innan de migreras. 
* Virtuella nätverk, gatewayenheter och associerade distributioner i det virtuella nätverket som inte ingår i samma prenumeration som ExpressRoute-kretsen kräver vissa avbrott för att slutföra migreringen. Det sista avsnittet i dokumentet beskriver steg ska följas för att migrera resurser.
* Ett virtuellt nätverk med både ExpressRoute-Gateway och VPN-Gateway kan inte migreras.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Flytta en ExpressRoute-krets från classic till Resource Manager
Du måste flytta en ExpressRoute-krets från klassiskt Resource Manager-miljön innan du försöker migrera resurser som är kopplade till ExpressRoute-kretsen. För att åstadkomma detta finns i följande artiklar:

* Granska informationen som ges enligt [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md).
* [Flytta en krets från classic till Resource Manager med hjälp av Azure PowerShell](expressroute-howto-move-arm.md).
* Använda Azure Service Management portal. Du kan följa arbetsflödet [skapa en ny ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och välj alternativet för import. 

Den här åtgärden innebär inte någon avbrottstid. Du kan fortsätta att överföra data mellan din lokala och Microsoft medan migreringen pågår.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrera associerade distributioner, gateways och virtuella nätverk

De steg du följer för att migrera beror på om dina resurser finns i samma prenumeration, olika prenumerationer eller båda.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrera virtuella nätverk, gateways och associerade distributioner i samma prenumeration som ExpressRoute-krets
Det här avsnittet beskriver de steg som ska följas för att migrera ett virtuellt nätverk, -gateway och associerade distributioner i samma prenumeration som ExpressRoute-kretsen. Inget driftstopp är associerad med den här migreringen. Du kan fortsätta att använda alla resurser via migreringsprocessen. Management-plan är låst medan migreringen pågår. 

1. Se till att ExpressRoute-kretsen har flyttats från klassiskt Resource Manager-miljön.
2. Se till att det virtuella nätverket har förberetts på rätt sätt för migreringen.
3. Registrera prenumerationen för resursmigrering. Använd följande PowerShell-fragment för att registrera prenumerationen för resursmigrering:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Validera, förbereda och migrera. Om du vill flytta det virtuella nätverket, Använd följande PowerShell-fragment:

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
* [Plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Vanliga frågor och svar: Plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Granska de vanligaste migreringsfel och åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
