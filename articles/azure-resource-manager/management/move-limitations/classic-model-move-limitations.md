---
title: Flytta Azure Classic-distributionsresurser
description: Använd Azure Resource Manager för att flytta klassiska distributionsresurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485291"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Flytta vägledning för resurser för klassiska distributionsmodeller

Stegen för att flytta resurser som distribueras via den klassiska modellen skiljer sig åt beroende på om du flyttar resurserna i en prenumeration eller till en ny prenumeration.

## <a name="move-in-the-same-subscription"></a>Flytta i samma prenumeration

När resurser flyttas från en resursgrupp till en annan resursgrupp inom samma prenumeration gäller följande begränsningar:

* Det går inte att flytta virtuella nätverk (klassiska).
* Virtuella datorer (klassiska) måste flyttas med molntjänsten.
* Molntjänsten kan bara flyttas när flytten innehåller alla dess virtuella datorer.
* Endast en molntjänst kan flyttas åt gången.
* Endast ett lagringskonto (klassiskt) kan flyttas åt gången.
* Lagringskonto (klassiskt) kan inte flyttas i samma åtgärd med en virtuell dator eller en molntjänst.

Om du vill flytta klassiska resurser till en ny resursgrupp inom samma prenumeration använder du [standardflyttåtgärderna](../move-resource-group-and-subscription.md) via portalen, Azure PowerShell, Azure CLI eller REST API. Du använder samma åtgärder som du använder för att flytta Resource Manager-resurser.

## <a name="move-across-subscriptions"></a>Flytta mellan prenumerationer

När resurser flyttas till en ny prenumeration gäller följande begränsningar:

* Alla klassiska resurser i prenumerationen måste flyttas i samma åtgärd.
* Målprenumerationen får inte ha några andra klassiska resurser.
* Flytten kan endast begäras via ett separat REST API för klassiska drag. Standardkommandona för Resurshanteraren fungerar inte när klassiska resurser flyttas till en ny prenumeration.

Om du vill flytta klassiska resurser till en ny prenumeration använder du REST-åtgärder som är specifika för klassiska resurser. Så här använder du REST:

1. Kontrollera om källprenumerationen kan delta i ett flytt med flera prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I organet för begäran, inkludera:

   ```json
   {
    "role": "source"
   }
   ```

     Svaret för valideringen är i följande format:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Kontrollera om målprenumerationen kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I organet för begäran, inkludera:

   ```json
   {
    "role": "target"
   }
   ```

     Svaret är i samma format som verifieringen av källprenumerationen.
1. Om båda prenumerationerna klarar valideringen flyttar du alla klassiska resurser från en prenumeration till en annan prenumeration med följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    I organet för begäran, inkludera:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Åtgärden kan pågå i flera minuter.

## <a name="next-steps"></a>Nästa steg

Om du har problem med att flytta klassiska resurser kontaktar du [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](../move-resource-group-and-subscription.md).
