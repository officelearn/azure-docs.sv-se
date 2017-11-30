---
title: "Återställa en misslyckad Azure ExpressRoute-krets: PowerShell | Microsoft Docs"
description: "Den här artikeln hjälper dig att återställa en ExpressRoute-krets som är i ett felaktigt tillstånd."
documentationcenter: na
services: expressroute
author: anzaman
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 0e017200193de3e4a02275cec3b09c32f1fa5c31
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="reset-a-failed-expressroute-circuit"></a>Återställa en misslyckad ExpressRoute-krets

När en åtgärd på en ExpressRoute-krets inte slutförs korrekt kan kretsen gå in i en '' feltillstånd. Den här artikeln hjälper dig att återställa en misslyckad Azure ExpressRoute-krets.

## <a name="reset-a-circuit"></a>Återställa en krets

1. Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Login-AzureRmAccount
  ```
3. Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Kör följande kommandon för att återställa en krets som är i ett felaktigt tillstånd:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Nu bör kretsen felfritt. Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om kretsen är fortfarande i ett felaktigt tillstånd.

## <a name="next-steps"></a>Nästa steg

Öppna ett supportärende med [Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om det fortfarande uppstår problem.
