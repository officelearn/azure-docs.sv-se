---
title: Iterativ appdesign i Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor.  LUIS appar kräver design iterationer för att träna LUIS för att få den bästa Extraheringen av data.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 2bd30aad995e9d1f334988652477f8b017c187b9
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638298"
---
# <a name="authoring-cycle"></a>Redigeringscykel
LUIS lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor. 

![Redigeringscykel](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Att skapa en LUIS-modell
Modellens syftet är att ta reda på vad du frågar efter (avsikt eller avsikt) och vilka delar av frågan ger information (entiteter) som hjälper dig att avgöra svaret. 

Modellen måste vara specifika för app-domänen för att kunna fastställa ord och fraser som är relevanta samt vanliga ord ordning. 

Modellen innehåller avsikt, entiteter. 

## <a name="add-training-examples"></a>Lägga till exempel för utbildning
LUIS måste exempel yttranden i avsikter. Exemplen behöver tillräckligt med variant av word valmöjligheter och ordföljden för att kunna avgöra vilken avsikt uttryck som är avsedd för. Varje exempel-uttryck måste ha alla nödvändiga data som är märkt som entiteter. 

Du instruera LUIS för att ignorera yttranden som inte är relevanta för din app domän genom att tilldela uttryck till den **ingen** avsikt. Du behöver inte ange några ord eller fraser som du inte behöver dras ut från ett uttryck ska förses med. Det finns ingen etikett efter ord eller fraser att ignorera. 

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
När du har 10 – 15 olika yttranden i varje syftet med de nödvändiga entiteter som är märkt, träna och publicera. Använd länken från publicera lyckade meddelande, för att hämta dina slutpunkter. Se till att skapa din app och publicera dina appar så att den är tillgänglig i den [endpoint regioner](luis-reference-regions.md) du behöver. 

## <a name="https-endpoint-testing"></a>Testa HTTPS-slutpunkt
Du kan testa LUIS-appen från HTTPS-slutpunkten. Testa från slutpunkten kan LUIS för att välja yttranden med låg exakthet för granskning.  

## <a name="recycle"></a>Papperskorgen
När du är klar med en cykel av redigeringen av kan börja du igen. Börja med att slutpunkten yttranden LUIS markeras med låg exakthet. Kontrollera dessa yttranden för både avsikten och entiteten. När du har läst yttranden, får granska listan vara tomt.  

## <a name="batch-testing"></a>Batch-testning
Batch testning är ett sätt att se hur många exempel yttranden poängsätts av LUIS. Exemplen ska vara ny till LUIS och bör vara korrekt är märkt med avsikt och entiteter som du vill att LUIS för att hitta. Testresultaten visar hur väl LUIS utför på den uppsättningen yttranden. 

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [samarbete](luis-concept-collaborator.md).