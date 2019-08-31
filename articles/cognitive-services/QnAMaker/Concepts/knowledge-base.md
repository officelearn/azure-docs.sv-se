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
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78b04a06b71590a79180d1ae367e7d059e1b84a3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195239"
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

### <a name="ranker-process"></a>Rangordnings process

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

## <a name="http-request-and-response-with-endpoint"></a>HTTP-begäran och-svar med slut punkten
När du publicerar din kunskaps bas skapar tjänsten en REST-baserad HTTP- **slutpunkt** som kan integreras i ditt program, ofta en chatt-robot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Förfrågan om användar frågan för att generera ett svar

En **användar fråga** är den fråga som slutanvändaren ber om kunskaps basen, till exempel, `How do I add a collaborator to my app?`. Frågan är ofta i ett naturligt språk format eller ett fåtal nyckelord som representerar frågan, till exempel, `help with collaborators`. Frågan skickas till din kunskap från en HTTP- **begäran** i klient programmet.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Du styr svaret genom att ställa in egenskaper som [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)och [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Använd [konversations kontext](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) med [funktioner för flera funktioner](../how-to/multiturn-conversation.md) för att hålla konversationen att förfina frågorna och Svaren för att hitta rätt och slutgiltigt svar.

### <a name="the-response-from-a-call-to-generate-answer"></a>Svaret från ett anrop till att generera svar

HTTP- **svaret** är det svar som hämtats från kunskaps basen, baserat på den bästa matchningen för en specifik användar fråga. Svaret innehåller svaret och förutsägelse poängen. Om du har begärt fler än ett Top svar, med `top` egenskapen, får du fler än ett Top svar, var och en med poäng. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Test-och produktions kunskaps bas
En kunskapsbas är lagringsplatsen för frågor och svar skapas, underhålls och användas via QnA Maker. Varje QnA Maker-nivå kan användas för flera kunskapsbaser.

En kunskapsbas har två tillstånd - Test och publicerats. 

**Test kunskaps basen** är den version som redige ras, sparas och testas för precision och fullständig svars information. Ändringar som gjorts i kunskapsbasen test påverkar inte slutanvändaren av ditt program/chattrobot. Test kunskaps basen kallas `test` i http-begäran. 

Den **publicerade kunskaps basen** är den version som används i chatten/programmet. Åtgärd för att publicera en kunskapsbas placerar innehållet i kunskapsbasen Test i den publicerade versionen av kunskapsbasen. Eftersom publicerade kunskapsbasen är den version som används i programmet via slutpunkten kan man att säkerställa att innehållet är korrekt och väl testade. Den publicerade kunskaps basen kallas `prod` i http-begäran. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Livscykeln för utveckling av en kunskapsbas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se också

[Översikt över QnA Maker](../Overview/overview.md)

Skapa och redigera kunskaps bas med: 
* [REST-API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generera svar med: 
* [REST-API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
