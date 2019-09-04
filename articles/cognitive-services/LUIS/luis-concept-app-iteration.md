---
title: Design av iterativa appar – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256918"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Redigerings cykel för LUIS-appen
LUIS lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor. 

![Redigeringscykel](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Att skapa en LUIS-modell
Modellens syftet är att ta reda på vad du frågar efter (avsikt eller avsikt) och vilka delar av frågan ger information (entiteter) som hjälper dig att avgöra svaret. 

Modellen måste vara specifika för app-domänen för att kunna fastställa ord och fraser som är relevanta samt vanliga ord ordning. 

Modellen kräver avsikter och _ska ha_ entiteter. 

## <a name="add-training-examples"></a>Lägga till exempel för utbildning
LUIS måste exempel yttranden i avsikter. Exemplen behöver tillräckligt med variant av word valmöjligheter och ordföljden för att kunna avgöra vilken avsikt uttryck som är avsedd för. Varje exempel-uttryck måste ha alla nödvändiga data som är märkt som entiteter. 

Du instruera LUIS för att ignorera yttranden som inte är relevanta för din app domän genom att tilldela uttryck till den **ingen** avsikt. Du behöver inte ange några ord eller fraser som du inte behöver dras ut från ett uttryck ska förses med. Det finns ingen etikett efter ord eller fraser att ignorera. 

## <a name="train-and-publish-the-app"></a>Träna och publicera appen
När du har 15 till 30 olika yttranden i varje avsikt, med de entiteter som krävs, måste du [träna](luis-how-to-train.md) och sedan [publicera](luis-how-to-publish-app.md). Använd länken från publicera lyckade meddelande, för att hämta dina slutpunkter. Se till att du skapar och publicerar din app så att den är tillgänglig i de [slut punkts regioner](luis-reference-regions.md) du behöver. 

## <a name="https-prediction-endpoint-testing"></a>Slut punkts testning för HTTPS förutsägelse
Du kan testa din LUIS-app från HTTPS förutsägelse-slutpunkten. Om du testar från förutsägelse slut punkten kan LUIS välja valfri yttranden med låg exakthet för [granskning](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Papperskorgen

När du är klar med en cykel av redigeringen av kan börja du igen. Börja med att [Granska förutsägelse slut punkt yttranden](luis-how-to-review-endpoint-utterances.md) Luis markerad med låg exakthet. Kontrollera dessa yttranden för både avsikten och entiteten. När du har läst yttranden, får granska listan vara tomt.  

Överväg att [klona](luis-concept-version.md#clone-a-version) den aktuella versionen till en ny version och börja sedan redigerings ändringarna i den nya versionen. 

## <a name="batch-testing"></a>Batch-testning

[Batch-testning](luis-concept-batch-test.md) är ett sätt att se hur många exempel yttranden som beräknas av Luis. Exemplen ska vara ny till LUIS och bör vara korrekt är märkt med avsikt och entiteter som du vill att LUIS för att hitta. Testresultaten visar hur väl LUIS utför på den uppsättningen yttranden. 

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [samarbete](luis-concept-keys.md).
