---
title: Fördefinierade modeller
titleSuffix: Language Understanding - Azure Cognitive Services
description: Fördefinierade modeller ger domäner, avsikter, yttranden och entiteter. Du kan påbörja din app med en fördefinierade domän eller lägga till en domän som är relevanta för din app senare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 75644fd94e6e00e92547cd9e1d42b26c6a55807f
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910636"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Fördefinierade modeller för domänen och avsikt entitet

Fördefinierade modeller ger domäner, avsikter, yttranden och entiteter. Du kan påbörja din app med en fördefinierade domän eller lägga till en domän som är relevanta för din app senare. 

## <a name="types-of-prebuilt-models"></a>Typer av fördefinierade modeller

Det finns 3 typer av fördefinierade modeller LUIS tillhandahåller. Varje modell kan läggas till i din app när som helst. 

|Modelltypen|Inkluderar|
|--|--|
|Domain|Avsikter yttranden, entiteter|
|Avsikter|Avsikter yttranden|
|Entiteter|Endast entiteter| 

## <a name="prebuilt-domains"></a>Fördefinierade domäner

Språkförståelse (LUIS) erbjuder *fördefinierade domäner*, som är färdiga uppsättningar [avsikter](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klientprogram. 

Fördefinierade domäner är tränade och redo att lägga till i din LUIS-app. Avsikter och entiteter i en fördefinierade domän är helt anpassningsbar när du har lagt till dem i din app. 

Om du startar från att anpassa en hel fördefinierade domän, ta bort avsikter och entiteter som din app inte behöver använda. Du kan också lägga till vissa avsikter eller entiteter som redan innehåller fördefinierade domänen. Exempel: Om du använder den **händelser** fördefinierade domänen för en händelse sport-app, kan du för att lägga till entiteter för sportklubbar. När du startar [att tillhandahålla yttranden](luis-how-to-add-example-utterances.md) till LUIS, ingår villkor som är specifika för din app. LUIS lär sig att identifiera dem och anpassar fördefinierade domänens avsikter och entiteter till appens behov. 

> [!TIP]
> Avsikter och entiteter i en fördefinierade domän fungerar bäst tillsammans. Är det bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> Den färdiga verktyg-domänen har avsikter som du kan anpassa för användning i valfri domän. Du kan till exempel lägga till `Utilities.Repeat` till din app och träna identifiera vilka åtgärder som användaren kanske vill upprepa i ditt program. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierade domän avsikt

Du kanske upptäcker att en fördefinierade domän innehåller ett intent som liknar ett intent som du vill ha i din LUIS-app, men du vill att den fungerar på olika sätt. Till exempel den **platser** fördefinierade domänen innehåller en `MakeReservation` avsikt för att göra en restaurang reservation, men du vill att din app att använda den avsikten för att göra hotell reservationer. I så fall kan du kan ändra beteendet för detta syfte genom att tillhandahålla yttranden till LUIS om att skapa hotell reservationer och märkning dem med hjälp av den `MakeReservation` avsiktlig, så sedan LUIS kan vara modellkomponenten för att identifiera den `MakeReservation` avsikt i en begäran om att boka ett hotell .

Du hittar en fullständig lista över fördefinierade domäner i den [fördefinierade domäner referens](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Fördefinierade avsikter

LUIS innehåller fördefinierade avsikter och deras yttranden. Avsikter kan läggas till utan att lägga till hela domänen. Att lägga till en avsikt är processen att lägga till en avsikt och sitt yttranden. Både avsiktlig namn och uttryck listan kan ändras.  

## <a name="prebuilt-entities"></a>Fördefinierade entiteter

LUIS innehåller en uppsättning fördefinierade entiteter för att identifiera vanliga typer av information som datum, tider, nummer, mätning av faktisk användning och valuta. Fördefinierade entitet support varierar beroende på kultur LUIS-appen. En fullständig lista över de fördefinierade entiteter som LUIS stöder, bland annat stöd av kulturen, finns i den [fördefinierade Entitetsreferens](./luis-reference-prebuilt-entities.md).

När en entitet som är färdiga ingår i ditt program, med dess förutsägelser i ditt publicerade program. Beteendet för förskapade entiteter är förtränade och **kan** ändras. Följ dessa steg om du vill se hur det fungerar en fördefinierade entitet:

> [!NOTE]
> **Builtin.datetime** är inaktuell. Ersätter [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), som tillhandahåller igenkänning av datum- och tidsintervall, samt bättre tolkning av tvetydiga datum och tider.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [Lägg till fördefinierade entiteter](luis-prebuilt-entities.md) till din app.