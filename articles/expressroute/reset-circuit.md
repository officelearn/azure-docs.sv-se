---
title: 'Återställa en misslyckad krets-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att återställa en ExpressRoute-krets som är i ett felaktigt tillstånd.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7df96f34ee408c0a6d26b27adbac7351c9ab937f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393096"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Återställ en ExpressRoute-krets som misslyckats

När en åtgärd på en ExpressRoute-krets inte slutförs kan kretsen hamna i ett misslyckat tillstånd. Den här artikeln hjälper dig att återställa en misslyckad Azure ExpressRoute-krets.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Återställa en krets

1. Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

2. Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att ansluta:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Ange den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Kör följande kommandon för att återställa en krets som är i ett felaktigt tillstånd:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Kretsen bör nu vara felfri. Öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om kretsen fortfarande är i ett felaktigt tillstånd.

## <a name="next-steps"></a>Nästa steg

Öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det fortfarande uppstår problem.
