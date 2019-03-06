---
title: 'Återställa en misslyckad krets - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att återställa en ExpressRoute-krets som är i ett felaktigt tillstånd.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 98a4e8a75958b377cdbeff353db89b1cb40dea92
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406617"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Återställa en misslyckad ExpressRoute-krets

När en åtgärd på en ExpressRoute-krets inte slutförs korrekt kan kretsen gå in i tillståndet ”misslyckades”. Den här artikeln hjälper dig att återställa en misslyckad Azure ExpressRoute-krets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Nu bör kretsen felfritt. Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om kretsen är fortfarande i ett felaktigt tillstånd.

## <a name="next-steps"></a>Nästa steg

Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.
