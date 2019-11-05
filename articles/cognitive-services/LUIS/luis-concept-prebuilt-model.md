---
title: Färdiga modeller – LUIS
titleSuffix: Azure Cognitive Services
description: Färdiga modeller tillhandahåller domäner, avsikter, yttranden och entiteter. Du kan starta din app med en fördefinierad domän eller lägga till en relevant domän i appen senare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: d9cb86c1c19649052e4796fd0a8909ce08381d55
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487580"
---
# <a name="prebuilt-models"></a>Fördefinierade modeller

Färdiga modeller tillhandahåller domäner, avsikter, yttranden och entiteter. Du kan starta din app med en fördefinierad modell eller lägga till en relevant modell i appen senare. 

## <a name="types-of-prebuilt-models"></a>Typer av förbyggda modeller

LUIS tillhandahåller tre typer av förinställda modeller. Varje modell kan när som helst läggas till i din app. 

|Modell typ|Innehåller|
|--|--|
|[Domänsuffix](luis-reference-prebuilt-domains.md)|Avsikter, yttranden, entiteter|
|Avsikter|Avsikter, yttranden|
|[Poster](luis-reference-prebuilt-entities.md)|Endast entiteter| 

## <a name="prebuilt-domains"></a>Fördefinierade domäner

Language Understanding (LUIS) ger *färdiga domäner*som är förtränade modeller av [avsikter](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klient program. 

De förinställda domänerna är utbildade och redo att läggas till i LUIS-appen. Avsikter och entiteter i en fördefinierad domän är helt anpassningsbara när du har lagt till dem i din app. 

> [!TIP]
> Avsikter och entiteter i en fördefinierad domän fungerar bäst tillsammans. Det är bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> De verktyg som är inbyggda i den färdiga domänen har intenter som du kan anpassa för användning i vilken domän som helst. Du kan till exempel lägga till `Utilities.Repeat` i din app och träna den att identifiera vilka åtgärder som användaren kan vilja upprepa i ditt program. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierad domän avsikt

Du kanske upptäcker att en fördefinierad domän innehåller en avsikt som liknar en avsikt som du vill ha i din LUIS-app, men du vill att den ska bete sig annorlunda. **Till exempel** tillhandahåller en fördefinierad domän en `MakeReservation` avsikt för att skapa en restaurang reservation, men du vill att appen ska använda den avsikten för att göra hotell reservationer. I så fall kan du ändra beteendet för avsikten genom att lägga till exempel-yttranden till avsikten med att göra hotell reservationer. Sedan omtränade appen. 

Du hittar en fullständig lista över de förinställda domänerna i den [fördefinierade domän referensen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Färdiga avsikter

LUIS tillhandahåller färdiga syften och deras yttranden, från de förinställda domänerna. Avsikter kan läggas till utan att du lägger till hela domänen. Att lägga till ett avsikt är att lägga till en avsikt och dess yttranden i din app. Både namnet på avsikten och uttryck-listan kan ändras.  

## <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information, t. ex. datum, tid, siffror, mätningar och valuta. Stöd för inbyggd entitet varierar beroende på LUIS-appens kultur. En fullständig lista över de färdiga entiteter som LUIS stöder, inklusive stöd för kultur, finns i den [fördefinierade enhets referensen](./luis-reference-prebuilt-entities.md).

När en fördefinierad entitet ingår i ditt program inkluderas dess förutsägelser i ditt publicerade program. Beteendet för förinställda entiteter är förtränat och **kan inte** ändras. 

> [!NOTE]
> **Builtin. DateTime** är föråldrad. Den ersätts av [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), som ger igenkänning av datum-och tidsintervall, samt förbättrad igenkänning av tvetydiga datum och tider.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [lägger till fördefinierade entiteter](luis-prebuilt-entities.md) i din app.
