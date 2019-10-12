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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264383"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Fördefinierad domän, avsikt och enhets modeller

Färdiga modeller tillhandahåller domäner, avsikter, yttranden och entiteter. Du kan starta din app med en fördefinierad domän eller lägga till en relevant domän i appen senare. 

## <a name="types-of-prebuilt-models"></a>Typer av förbyggda modeller

Det finns tre typer av förbyggda modeller LUIS tillhandahåller. Varje modell kan när som helst läggas till i din app. 

|Modell typ|Innehåller|
|--|--|
|Domain|Avsikter, yttranden, entiteter|
|Avsikter|Avsikter, yttranden|
|Entiteter|Endast entiteter| 

## <a name="prebuilt-domains"></a>Fördefinierade domäner

Language Understanding (LUIS) innehåller *fördefinierade domäner*, som är färdiga uppsättningar av [avsikter](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klient program. 

De förinställda domänerna är utbildade och redo att läggas till i LUIS-appen. Avsikter och entiteter i en fördefinierad domän är helt anpassningsbara när du har lagt till dem i din app. 

Om du börjar anpassa en hel fördefinierad domän tar du bort de avsikter och entiteter som din app inte behöver använda. Du kan också lägga till några avsikter eller entiteter i uppsättningen som redan har skapats i den inbyggda domänen. Om du till exempel **använder en** fördefinierad domän för en idrotts Event-app kan du lägga till entiteter för idrotts lag lag. När du börjar [tillhandahålla yttranden](luis-how-to-add-example-utterances.md) till Luis inkluderar du termer som är speciella för din app. LUIS lär sig att identifiera dem och skapa färdiga domän intentor och entiteter för din apps behov. 

> [!TIP]
> Avsikter och entiteter i en fördefinierad domän fungerar bäst tillsammans. Det är bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> De verktyg som är inbyggda i den färdiga domänen har intenter som du kan anpassa för användning i vilken domän som helst. Du kan till exempel lägga till `Utilities.Repeat` till din app och träna den att identifiera vilka åtgärder som användaren kan vilja upprepa i ditt program. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierad domän avsikt

Du kanske upptäcker att en fördefinierad domän innehåller en avsikt som liknar en avsikt som du vill ha i din LUIS-app, men du vill att den ska bete sig annorlunda. **Till exempel** tillhandahåller en fördefinierad domän en @no__t 1-avsikt för att göra en restaurang reservation, men du vill att din app ska använda den avsikten för att göra hotell reservationer. I så fall kan du ändra beteendet för avsikten genom att tillhandahålla yttranden till LUIS om att göra Hotell Bokningar och etikettera dem med hjälp av metoden `MakeReservation`, så LUIS kan sedan återanvändas för att identifiera `MakeReservation` avsikten i en begäran om att boka ett hotell.

Du hittar en fullständig lista över de förinställda domänerna i den [fördefinierade domän referensen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Färdiga avsikter

LUIS tillhandahåller färdiga syften och deras yttranden. Avsikter kan läggas till utan att du lägger till hela domänen. När du lägger till ett avsikts sätt är processen att lägga till en avsikt och dess yttranden. Både namnet på avsikten och uttryck-listan kan ändras.  

## <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information, t. ex. datum, tid, siffror, mätningar och valuta. Stöd för inbyggd entitet varierar beroende på LUIS-appens kultur. En fullständig lista över de färdiga entiteter som LUIS stöder, inklusive stöd för kultur, finns i den [fördefinierade enhets referensen](./luis-reference-prebuilt-entities.md).

När en fördefinierad entitet ingår i ditt program inkluderas dess förutsägelser i ditt publicerade program. Beteendet för förinställda entiteter är förtränat och **kan inte** ändras. 

> [!NOTE]
> **Builtin. DateTime** är föråldrad. Den ersätts av [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), som ger igenkänning av datum-och tidsintervall, samt förbättrad igenkänning av tvetydiga datum och tider.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [lägger till fördefinierade entiteter](luis-prebuilt-entities.md) i din app.
