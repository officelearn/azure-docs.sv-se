---
title: Villkorsuttryck för Azure CDN - Verizon Premium regelmotor
description: Referensdokumentation för Azure CDN från Verizon Premium regler motor match villkor och funktioner.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253517"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN från Verizon Premium regler motor villkorliga uttryck

I den här artikeln visas detaljerade beskrivningar av [CDN-regelmotorn](cdn-verizon-premium-rules-engine.md)(Conditional Expressions for Azure Content Delivery Network).

Den första delen av en regel är villkorsuttrycket.

Villkorsstyrdt uttryck | Beskrivning
-----------------------|-------------
IF | Ett IF-uttryck är alltid en del av den första satsen i en regel. Precis som alla andra villkorsuttryck måste den här IF-satsen associeras med en matchning. Om inga ytterligare villkorsuttryck har definierats avgör den här matchningen vilket kriterium som måste uppfyllas innan en uppsättning funktioner kan tillämpas på en begäran.
OCH OM | Ett OCH IF-uttryck kan bara läggas till efter följande typer av villkorsuttryck:OM,OCH OM. Den anger att det finns ett annat villkor som måste uppfyllas för den ursprungliga IF-satsen.
ELSE OM| Ett ELSE IF-uttryck anger ett alternativt villkor som måste uppfyllas innan en uppsättning funktioner som är specifika för den här ELSE IF-satsen äger rum. Förekomsten av en ELSE IF-sats anger slutet på föregående utdrag. Det enda villkorsuttryck som kan placeras efter en ELSE IF-sats är en annan ELSE IF-sats. Detta innebär att en ELSE IF-sats endast får användas för att ange ett enda ytterligare villkor som måste uppfyllas.

**Exempel** ![: CDN matchningsvillkor](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   > Exempel: En catch-all-regel skyddar alla begäranden via tokenbaserad autentisering. En annan regel kan skapas direkt under den för att göra ett undantag för vissa typer av begäranden.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för regler motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regler motor match villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regler motorfunktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standard-HTTP-beteende med hjälp av regelmotorn](cdn-verizon-premium-rules-engine.md)