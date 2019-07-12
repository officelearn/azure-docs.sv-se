---
title: Azure CDN från Verizon Premium regelmotor – villkorliga uttryck | Microsoft Docs
description: Referensdokumentation för Azure CDN från Verizon Premium regelmotor – matchningsvillkor och funktioner.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593226"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN från Verizon Premium regler – villkorliga uttryck

Den här artikeln innehåller detaljerade beskrivningar av villkorliga uttryck för Azure Content Delivery Network (CDN) [regelmotor](cdn-verizon-premium-rules-engine.md).

Den första delen av en regel är villkorliga uttryck.

Villkorsuttryck | Beskrivning
-----------------------|-------------
IF | Ett IF-uttryck är alltid en del av den första instruktionen i en regel. Precis som alla andra villkorliga uttryck, måste IF-instruktionen vara associerad med en matchning. Om inga ytterligare villkorsuttryck har definierats anger den här matchar de villkor som måste uppfyllas innan en uppsättning funktioner som kan tillämpas på en begäran.
OCH OM | Ett uttryck och om kan bara läggas till när du har följande typer av villkorliga uttryck: om, och om. Det betyder att det finns ytterligare ett villkor som måste uppfyllas för att den inledande IF-instruktionen.
ELSE IF| Ett annat IF-uttryck anger ett alternativt villkor som måste uppfyllas innan en uppsättning funktioner som är specifika för den här ANNARS om instruktionen äger rum. Förekomsten av en annan IF-instruktionen anger slutet av den föregående instruktionen. Endast villkorlig uttrycket som du kan använda när du har en annan IF-instruktionen är en annan ANNARS om-instruktion. Det innebär att en annan IF-instruktionen bara kan användas till att ange ett enda ytterligare villkor som måste uppfyllas.

**Exempel**: ![CDN-matchningsvillkor](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   > Exempel: En regel för allomfattande skyddar alla förfrågningar via tokenbaserad autentisering. En annan regel som kan skapas direkt under den för att göra ett undantag för vissa typer av begäranden.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Regelmotor – referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-verizon-premium-rules-engine.md)