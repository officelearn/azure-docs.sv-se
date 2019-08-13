---
title: Knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955240"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Vad är en Maker kunskapsbaser?

En kunskapsbas med QnA Maker består av en uppsättning fråga/svar (frågor och svar)-par och valfria metadata som är associerade med varje par med frågor och svar.

## <a name="key-knowledge-base-concepts"></a>Viktiga kunskapsbas-begrepp

* **Frågor** – en fråga som innehåller text som bäst representerar en användarfråga. 
* **Svar** -svar är svaret som returneras när en användarfråga matchas med associerade frågan.  
* **Metadata** -Metadata är taggar som är associerad med ett par frågor och svar och visas i form av nyckel / värde-par. Metadata-Taggar används för att filtrera QnA-par och begränsa den mängd som frågan ska matchas över.

En enda QnA som representeras av ett numeriskt QnA-ID har flera varianter av en fråga (alternativa frågor) att mappas till ett enda svar. Dessutom kan varje sådant par ha flera associerade metadatafält: en nyckel och ett värde.

![QnA Maker kunskapsbaser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Kunskapsbas innehållsformat

När du mata in formaterade innehållet i en kunskapsbas försöker QnA Maker konvertering av innehåll till markdown. Läs [detta](https://aka.ms/qnamaker-docs-markdown-support) blogg om du vill förstå markdown formaterar klusterverifieringsrapportschemat de flesta chatt-klienter.

Metadatafält består av nyckel / värde-par avgränsade med kolon **(produkt: fragmentering)** . Både nyckel och värde måste vara endast text. Metadatanyckel får inte innehålla några blanksteg. Metadata stöder bara ett värde per nyckel.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hur QnA Maker bearbetar en användar fråga för att välja det bästa svaret

Den utbildade och [publicerade](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QNA Maker kunskaps basen tar emot en användar fråga, från en robot eller något annat klient program, i [GenerateAnswer-API: et](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Följande diagram illustrerar processen när användar frågan tas emot.

![Rangordnings processen för en användar fråga](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Processen beskrivs i följande tabell:

|Steg|Syfte|
|--|--|
|1|Klient programmet skickar användar frågan till [GenerateAnswer-API: et](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QNA Maker förbearbetar användar frågan med språk identifiering, stavnings kontroll och ord separatorer.|
|3|Den här förbearbetningen görs för att ändra användar fråga för bästa Sök resultat.|
|4|Den här ändrade frågan skickas till Azure Search index och tar emot `top` antalet resultat. Om rätt svar inte finns i dessa resultat ökar du värdet `top` något. Normalt är 10 för `top` Works i 90% av frågorna.|
|5|QnA Maker använder avancerade funktionalisering för att avgöra om den hämtade Azure Search resultat för användar frågan är korrekt. |
|6|Den tränade ranknings modellen använder funktions poängen, från steg 5, för att rangordna Azure Search resultat.|
|7|De nya resultaten returneras till klient programmet i rangordnings ordning.|
|||

Funktioner som används inkluderar, men är inte begränsade till semantik på Word-nivå, prioritet på term nivå i en sökkorpus och djup lärt semantiska modeller för att fastställa likhet och relevans mellan två text strängar.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Livscykeln för utveckling av en kunskapsbas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se också

[Översikt över QnA Maker](../Overview/overview.md)
