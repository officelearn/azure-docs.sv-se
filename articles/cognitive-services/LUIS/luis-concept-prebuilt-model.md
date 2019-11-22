---
title: Färdiga modeller – LUIS
titleSuffix: Azure Cognitive Services
description: Fördefinierade modeller ger domäner, avsikter, yttranden och entiteter. Du kan påbörja din app med en fördefinierade domän eller lägga till en domän som är relevanta för din app senare.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280842"
---
# <a name="prebuilt-models"></a>Fördefinierade modeller

Fördefinierade modeller ger domäner, avsikter, yttranden och entiteter. Du kan starta din app med en fördefinierad modell eller lägga till en relevant modell i appen senare. 

## <a name="types-of-prebuilt-models"></a>Typer av fördefinierade modeller

LUIS tillhandahåller tre typer av förinställda modeller. Varje modell kan läggas till i din app när som helst. 

|Modelltypen|Inkluderar|
|--|--|
|[Domän](luis-reference-prebuilt-domains.md)|Avsikter yttranden, entiteter|
|Avsikter|Avsikter yttranden|
|[Poster](luis-reference-prebuilt-entities.md)|Endast entiteter| 

## <a name="prebuilt-domains"></a>Fördefinierade domäner

Language Understanding (LUIS) ger färdiga *domäner*som är förtränade modeller av [avsikter](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klient program. 

Fördefinierade domäner är tränade och redo att lägga till i din LUIS-app. Avsikter och entiteter i en fördefinierad domän är helt anpassningsbara när du har lagt till dem i din app. 

> [!TIP]
> Avsikter och entiteter i en fördefinierade domän fungerar bäst tillsammans. Är det bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> Den färdiga verktyg-domänen har avsikter som du kan anpassa för användning i valfri domän. Du kan till exempel lägga till `Utilities.Repeat` till din app och träna identifiera vilka åtgärder som användaren kanske vill upprepa i ditt program. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierade domän avsikt

Du kanske upptäcker att en fördefinierade domän innehåller ett intent som liknar ett intent som du vill ha i din LUIS-app, men du vill att den fungerar på olika sätt. **Till exempel** tillhandahåller en fördefinierad domän en `MakeReservation` avsikt för att skapa en restaurang reservation, men du vill att appen ska använda den avsikten för att göra hotell reservationer. I så fall kan du ändra beteendet för avsikten genom att lägga till exempel yttranden i avsikten med att göra Hotell Bokningar och sedan träna appen. 

Du hittar en fullständig lista över fördefinierade domäner i den [fördefinierade domäner referens](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Fördefinierade avsikter

LUIS tillhandahåller färdiga syften och deras yttranden för varje fördefinierad domän. Avsikter kan läggas till utan att lägga till hela domänen. Att lägga till ett avsikt är att lägga till en avsikt och dess yttranden i din app. Både avsiktlig namn och uttryck listan kan ändras.  

## <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tider, nummer, mätning av faktisk användning och valuta. Fördefinierade entitet support varierar beroende på kultur LUIS-appen. En fullständig lista över de fördefinierade entiteter som LUIS stöder, bland annat stöd av kulturen, finns i den [fördefinierade Entitetsreferens](./luis-reference-prebuilt-entities.md).

När en entitet som är färdiga ingår i ditt program, med dess förutsägelser i ditt publicerade program. Beteendet för förskapade entiteter är förtränade och **kan** ändras. 

> [!NOTE]
> **Builtin.datetime** är inaktuell. Ersätter [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), som tillhandahåller igenkänning av datum- och tidsintervall, samt bättre tolkning av tvetydiga datum och tider.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [Lägg till fördefinierade entiteter](luis-prebuilt-entities.md) till din app.
