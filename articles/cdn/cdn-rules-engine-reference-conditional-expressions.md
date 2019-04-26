---
title: Azure CDN regelmotor – villkorliga uttryck | Microsoft Docs
description: Referensdokumentation för Azure CDN regelmotor – matchningsvillkor och funktioner.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 73c41b754c0aca5ddb1a49fcd2794aa41b2fa705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324202"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN regelmotor – villkorliga uttryck
Det här avsnittet innehåller detaljerade beskrivningar av villkorliga uttryck för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den första delen av en regel är villkorliga uttryck.

Villkorsuttryck | Beskrivning
-----------------------|-------------
IF | Ett IF-uttryck är alltid en del av den första instruktionen i en regel. Precis som alla andra villkorliga uttryck, måste IF-instruktionen vara associerad med en matchning. Om inga ytterligare villkorsuttryck har definierats anger den här matchar de villkor som måste uppfyllas innan en uppsättning funktioner som kan tillämpas på en begäran.
OCH OM | Ett uttryck och om kan bara läggas till när du har följande typer av villkorliga uttryck: om, och om. Det betyder att det finns ytterligare ett villkor som måste uppfyllas för att den inledande IF-instruktionen.
ELSE IF| Ett annat IF-uttryck anger ett alternativt villkor som måste uppfyllas innan en uppsättning funktioner som är specifika för den här ANNARS om instruktionen äger rum. Förekomsten av en annan IF-instruktionen anger slutet av den föregående instruktionen. Endast villkorlig uttrycket som du kan använda när du har en annan IF-instruktionen är en annan ANNARS om-instruktion. Det innebär att en annan IF-instruktionen bara kan användas till att ange ett enda ytterligare villkor som måste uppfyllas.

**Exempel**: ![CDN-matchningsvillkor](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel. Exempel: En regel för allomfattande skyddar alla förfrågningar via tokenbaserad autentisering. En annan regel som kan skapas direkt under den för att göra ett undantag för vissa typer av begäranden.

### <a name="next-steps"></a>Nästa steg
* [Översikt över Azure CDN](cdn-overview.md)
* [Regler – referens](cdn-rules-engine-reference.md)
* [Regelmotor – matchningsvillkor](cdn-rules-engine-reference-match-conditions.md)
* [Regler – funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-rules-engine.md)
