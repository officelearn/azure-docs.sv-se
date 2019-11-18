---
title: Flytta klassiska Azure-distributions resurser
description: Använd Azure Resource Manager för att flytta klassiska distributions resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150854"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Flytta vägledning för klassiska distributions modell resurser

Stegen för att flytta resurser som distribueras via den klassiska modellen beror på om du flyttar resurserna i en prenumeration eller till en ny prenumeration.

## <a name="move-in-the-same-subscription"></a>Flytta i samma prenumeration

När du flyttar resurser från en resursgrupp till en annan resursgrupp i samma prenumeration, gäller följande begränsningar:

* Virtuella nätverk (klassisk) kan inte flyttas.
* Virtuella datorer (klassiska) måste flyttas med Molntjänsten.
* Molntjänsten kan bara flyttas när flytten omfattar alla virtuella datorer.
* Endast en molntjänst kan flyttas åt gången.
* Endast en storage-konto (klassisk) kan flyttas åt gången.
* Storage-konto (klassisk) kan inte flyttas på samma gång med en virtuell dator eller en tjänst i molnet.

Om du vill flytta klassiska resurser till en ny resurs grupp i samma prenumeration använder du [standard flyttnings åtgärder](../resource-group-move-resources.md) via portalen, Azure PowerShell, Azure CLI eller REST API. Du kan använda samma åtgärder som du använder för att flytta Resurshanterarens resurser.

## <a name="move-across-subscriptions"></a>Flytta över prenumerationer

När du flyttar resurser till en ny prenumeration, gäller följande begränsningar:

* Alla klassiska resurser i prenumerationen måste flyttas på samma gång.
* Mål prenumerationen får inte ha några andra klassiska resurser.
* Flytten kan bara begäras via en separat REST-API för klassiska flyttar. Standardkommandon för Resource Manager-flytta fungerar inte när du flyttar klassiska resurser till en ny prenumeration.

Flytta klassiska resurser till en ny prenumeration genom att använda REST-åtgärder som är specifika för klassiska resurser. Gör så här om du vill använda REST:

1. Kontrollera om käll-prenumeration kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I begärandetexten, inkluderar du:

   ```json
   {
    "role": "source"
   }
   ```

     Svaret för valideringen har följande format:

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

     I begärandetexten, inkluderar du:

   ```json
   {
    "role": "target"
   }
   ```

     Svaret är i samma format som datakälla prenumerationen verifieringen.
1. Om båda prenumerationerna passerar valideringen flytta alla klassiska resurser från en prenumeration till en annan prenumeration med följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    I begärandetexten, inkluderar du:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Åtgärden kan ta flera minuter.

## <a name="next-steps"></a>Nästa steg

Kontakta [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)om du har problem med att flytta klassiska resurser.

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../resource-group-move-resources.md).
