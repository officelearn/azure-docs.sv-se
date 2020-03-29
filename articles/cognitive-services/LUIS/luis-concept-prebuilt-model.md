---
title: Fördefinierade modeller - LUIS
titleSuffix: Azure Cognitive Services
description: Fördefinierade modeller tillhandahåller domäner, avsikter, yttranden och entiteter. Du kan starta appen med en fördefinierad domän eller lägga till en relevant domän i appen senare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280842"
---
# <a name="prebuilt-models"></a>Fördefinierade modeller

Fördefinierade modeller tillhandahåller domäner, avsikter, yttranden och entiteter. Du kan starta appen med en färdig modell eller lägga till en relevant modell i appen senare. 

## <a name="types-of-prebuilt-models"></a>Typer av färdiga modeller

LUIS tillhandahåller tre typer av färdiga modeller. Varje modell kan läggas till i din app när som helst. 

|Typ av modell|Inkluderar|
|--|--|
|[Domain](luis-reference-prebuilt-domains.md)|Avsikter, yttranden, entiteter|
|Avsikter|Avsikter, yttranden|
|[Entiteter](luis-reference-prebuilt-entities.md)|Endast entiteter| 

## <a name="prebuilt-domains"></a>Fördefinierade domäner

Språkförståelse (LUIS) innehåller *fördefinierade domäner*, som är förutbildade modeller av avsikter och [entiteter](luis-concept-entity-types.md) som arbetar tillsammans för domäner eller vanliga kategorier av klientprogram. [intents](luis-how-to-add-intents.md) 

De fördefinierade domänerna är tränade och redo att lägga till i LUIS-appen. Avsikterna och entiteterna för en fördefinierad domän är helt anpassningsbara när du har lagt till dem i din app. 

> [!TIP]
> Avsikter och entiteter i en fördefinierad domän fungerar bäst tillsammans. Det är bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> Den fördefinierade domänen Verktyg har avsikter som du kan anpassa för användning i alla domäner. Du kan till `Utilities.Repeat` exempel lägga till i din app och träna den att känna igen de åtgärder som användaren kanske vill upprepa i ditt program. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierad domän avsikt

Du kanske upptäcker att en fördefinierad domän innehåller en avsikt som liknar en avsikt som du vill ha i LUIS-appen men du vill att den ska bete sig annorlunda. Till exempel **Places** innehåller den fördefinierade `MakeReservation` domänen Platser en avsikt att göra en restaurangbokning, men du vill att appen ska använda den avsikten för att göra hotellbokningar. I så fall kan du ändra beteendet för den avsikten genom att lägga till exempel yttranden till avsikten om att göra hotellbokningar och sedan träna om appen. 

Du hittar en fullständig lista över de fördefinierade domänerna i [referensen för fördefinierade domäner](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Fördefinierade avsikter

LUIS tillhandahåller fördefinierade avsikter och deras yttranden för var och en av dess fördefinierade domäner. Avsikter kan läggas till utan att lägga till hela domänen. Att lägga till en avsikt är processen att lägga till en avsikt och dess yttranden till din app. Både avsiktsnamnet och uttryckslistan kan ändras.  

## <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att känna igen vanliga typer av information, till exempel datum, tider, siffror, mått och valuta. Fördefinierat entitetsstöd varierar beroende på kulturen i LUIS-appen. En fullständig lista över de fördefinierade entiteter som LUIS stöder, inklusive stöd per kultur, finns i den [fördefinierade entitetsreferensen](./luis-reference-prebuilt-entities.md).

När en fördefinierad entitet ingår i ditt program inkluderas dess förutsägelser i ditt publicerade program. Beteendet för fördefinierade entiteter är förtränat och **kan inte** ändras. 

> [!NOTE]
> **builtin.datetime** är föråldrad. Den ersätts av [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), som ger erkännande av datum och tidsintervall, samt förbättrad igenkänning av tvetydiga datum och tider.

## <a name="next-steps"></a>Nästa steg

Läs om hur du [lägger till fördefinierade entiteter](luis-prebuilt-entities.md) i din app.
