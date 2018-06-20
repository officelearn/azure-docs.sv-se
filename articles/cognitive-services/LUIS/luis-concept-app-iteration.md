---
title: Förstå THOMAS iterativ app design - Azure | Microsoft Docs
description: THOMAS appar kräver design iterationer för att träna THOMAS att hämta den bästa Extraheringen av data.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: b7f8dd46dc8289322726934f330761b0f1ab94bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265945"
---
# <a name="authoring-cycle"></a>Redigera cykel
THOMAS lär sig bäst i en iterativ livscykel Modelländringar, utterance exempel, publicering och samlar in data från slutpunkten frågor. 

![Redigera cykel](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Skapa en THOMAS modell
Modellens syftet är att ta reda på vad användaren frågar efter (avsikt eller avsikten) och vilka delar av frågan ger information (enheter) som underlättar för svaret. 

Modellen måste vara specifika för appdomänen för att fastställa ord och fraser som är relevanta samt vanliga ord ordning. 

Modellen innehåller avsikt, entiteter. 

## <a name="add-training-examples"></a>Lägg till exempel utbildning
THOMAS måste exempel utterances i innehållet. Exemplen behöver tillräckligt med variation av word val och word ordning för att kunna avgöra vilken avsikt i utterance är avsett för. Varje exempel utterance måste ha alla nödvändiga data med etiketten som enheter. 

Du instruera THOMAS att ignorera utterances som inte är relevanta för din app domänen genom att tilldela utterance till den **ingen** avsikt. Ord eller fraser som du inte behöver hämtas utanför en utterance behöver inte ska förses med. Det finns ingen etikett för ord eller fraser att ignorera. 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>Träna och publicera appen
När du har 10 – 15 olika utterances i varje avsikten med nödvändiga entiteter med etiketten du träna THOMAS och sedan publicera för att få dina slutpunkter. Se till att skapa din app och publicera en app så att den är tillgänglig i den [endpoint regioner](luis-reference-regions.md) du behöver. 

## <a name="https-endpoint-testing"></a>Testning av HTTPS-slutpunkt
Du kan testa din app THOMAS HTTPS-slutpunkt som visas på den **[publicera](publishapp.md)** sidan. Testa från slutpunkten kan THOMAS att välja alla utterances med låg förtroende för granskning.  

## <a name="recycle"></a>Papperskorgen
När du är klar med en cykel med redigering av starta du den igen. Börja med att granska endpoint utterances THOMAS markerats med låg förtroende. Kontrollera dessa utterances för både avsikt och entiteten. När du har läst utterances, ska granska listan vara tomt.  

## <a name="batch-testing"></a>Testa batch
Testa batch är ett sätt att se hur många exempel utterances bedömas av THOMAS. Exemplen ska vara nya THOMAS och korrekt ska förses med avsikt och enheter som du vill använda THOMAS att hitta. Testresultaten visar hur väl THOMAS utförs på den uppsättningen utterances. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer begrepp om [samarbete](luis-concept-collaborator.md).

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains