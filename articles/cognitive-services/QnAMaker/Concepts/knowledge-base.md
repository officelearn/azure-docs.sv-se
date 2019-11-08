---
title: Kunskaps bas – QnA Maker
titleSuffix: Azure Cognitive Services
description: En QnA Maker kunskaps bas består av en uppsättning fråga-och-svar-par (QnA) och valfria metadata som är kopplade till varje QnA-par.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794902"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Vad är en QnA Maker kunskaps bas?

En QnA Maker kunskaps bas består av en uppsättning fråga-och-svar-par (QnA) och valfria metadata som är kopplade till varje QnA-par.

## <a name="key-knowledge-base-concepts"></a>Grundläggande begrepp för kunskaps bas

* **Frågor**: en fråga innehåller text som bäst representerar en användar fråga. 
* **Svar**: ett svar är svaret som returneras när en användar fråga matchas med den associerade frågan.  
* **Metadata**: metadata är taggar som är associerade med ett QNA-par och som representeras som nyckel/värde-par. Metadata-Taggar används för att filtrera QnA-par och begränsa den mängd som frågan ska matchas över.

En enskild QnA, som representeras av ett numeriskt QnA-ID, har flera varianter av en fråga (alternativa frågor) som alla mappar till ett enda svar. Dessutom kan varje sådant par ha flera associerade fält för metadata: en nyckel och ett värde.

![QnA Maker kunskaps baser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Innehålls format för kunskaps bas

När du matar in innehåll i en kunskaps bas försöker QnA Maker att konvertera innehållet till markdown. Läs [den här bloggen](https://aka.ms/qnamaker-docs-markdown-support) om du vill veta mer om de markdown-format som kan förstås av de flesta chatt-klienter.

Metadata-fält består av nyckel/värde-par avgränsade med kolon, till exempel Product: förstörare. Både nyckeln och värdet får endast vara text. Metadata-nyckeln får inte innehålla blank steg. Metadata stöder bara ett värde per nyckel.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hur QnA Maker bearbetar en användar fråga för att välja det bästa svaret

Den utbildade och [publicerade](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QNA Maker kunskaps basen tar emot en användar fråga, från en robot eller något annat klient program, i [GenerateAnswer-API: et](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Följande diagram illustrerar processen när användar frågan tas emot.

![Rangordnings processen för en användar fråga](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Rangordnings process

Processen beskrivs i följande tabell.

|Steg|Syfte|
|--|--|
|1|Klient programmet skickar användar frågan till [GenerateAnswer-API: et](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker förbearbetar användar frågan med språk identifiering, stavnings-och ord separatorer.|
|3|Den här förbearbetningen görs för att ändra användar frågan för bästa Sök resultat.|
|4|Den här ändrade frågan skickas till ett Azure Kognitiv sökning-index, som tar emot `top` antalet resultat. Om rätt svar inte finns i dessa resultat ökar du värdet för `top` något. Vanligt vis fungerar värdet 10 för `top` i 90% av frågorna.|
|5|QnA Maker använder avancerade funktionalisering för att avgöra om de hämtade Sök resultaten är korrekta för användar frågan. |
|6|Den tränade ranknings modellen använder funktions poängen, från steg 5, för att rangordna Azure Kognitiv sökning-resultatet.|
|7|De nya resultaten returneras till klient programmet i rangordnings ordning.|
|||

Funktioner som används inkluderar, men är inte begränsade till semantik på Word-nivå, prioritet på term nivå i en sökkorpus och djup lärt semantiska modeller för att fastställa likhet och relevans mellan två text strängar.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-begäran och-svar med slut punkten
När du publicerar din kunskaps bas skapar tjänsten en REST-baserad HTTP-slutpunkt som kan integreras i ditt program, ofta en chatt-robot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Förfrågan om användar frågan för att generera ett svar

En användar fråga är den fråga som slutanvändaren ber om kunskaps basen, t. ex. `How do I add a collaborator to my app?`. Frågan är ofta i ett naturligt språk format eller några nyckelord som representerar frågan, t. ex. `help with collaborators`. Frågan skickas till din kunskaps bas från en HTTP-begäran i klient programmet.

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

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Svaret från ett anrop för att generera ett svar

HTTP-svaret är det svar som hämtats från kunskaps basen, baserat på den bästa matchningen för en specifik användar fråga. Svaret innehåller svaret och förutsägelse poängen. Om du har begärt fler än ett Top svar med egenskapen `top` får du fler än ett Top svar, var och en med poäng. 

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
En kunskaps bas är en lagrings plats för frågor och svar som skapats, bibehålls och används via QnA Maker. Varje QnA Maker nivå kan användas för flera kunskaps baser.

En kunskaps bas har två tillstånd: *testa* och *publicerade*.

*Test kunskaps basen* är den version som redige ras, sparas och testas för precision och fullständig svars information. Ändringar som görs i test kunskaps basen påverkar inte slutanvändaren för ditt program eller chatt-robot. Test kunskaps basen kallas `test` i HTTP-begäran. 

Den *publicerade kunskaps basen* är den version som används i din chatt eller ditt program. Åtgärden att publicera en kunskaps bas ger innehållet i test kunskaps basen i den publicerade versionen av kunskaps basen. Eftersom den publicerade kunskaps basen är den version som programmet använder genom slut punkten kontrollerar du att innehållet är korrekt och testat. Den publicerade kunskaps basen kallas `prod` i HTTP-begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utvecklings livs cykel för en kunskaps bas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se även

[Översikt över QnA Maker](../Overview/overview.md)

Skapa och redigera en kunskaps bas med: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generera ett svar med: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
