---
title: Fråga kunskapsbasen - QnA Maker
description: En kunskapsbas måste publiceras. När kunskapsbasen har publicerats efterfrågas slutpunkten för körningsprognos med hjälp av generateAnswer API.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220725"
---
# <a name="query-the-knowledge-base-for-answers"></a>Fråga kunskapsbasen för svar

En kunskapsbas måste publiceras. När kunskapsbasen har publicerats efterfrågas slutpunkten för körningsprognos med hjälp av generateAnswer API. Frågan innehåller frågetexten och andra inställningar för att hjälpa QnA Maker att välja bästa möjliga matchning till ett svar.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hur QnA Maker behandlar en användarfråga för att välja det bästa svaret

Den utbildade och [publicerade](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker-kunskapsbasen tar emot en användarfråga, från en bot eller ett annat klientprogram, på [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Följande diagram illustrerar processen när användarfrågan tas emot.

![Rangordningsmodellprocessen för en användarfråga](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker-process

Processen förklaras i följande tabell.

|Steg|Syfte|
|--|--|
|1|Klientprogrammet skickar användarfrågan till [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker förbearbetar användarfrågan med språkidentifiering, spellers och ordbrytare.|
|3|Den här förbearbetningen vidtas för att ändra användarfrågan för bästa sökresultat.|
|4|Den här ändrade frågan skickas till ett Azure `top` Cognitive Search Index, som tar emot antalet resultat. Om det rätta svaret inte finns i dessa `top` resultat ökar du värdet på något. I allmänhet fungerar värdet 10 för `top` i 90 % av frågorna.|
|5|QnA Maker använder syntaktisk och semantisk baserad featurization för att fastställa likheten mellan användarfrågan och de hämtade QnA-resultaten.|
|6|Den maskininlärda rankermodellen använder de olika funktionerna, från steg 5, för att bestämma konfidenspoängen och den nya rangordningen.|
|7|De nya resultaten returneras till klientprogrammet i rangordnad ordning.|
|||

Funktioner som används inkluderar men är inte begränsade till ordnivå semantik, term-nivå betydelse i en korpus, och djupt inlärda semantiska modeller för att avgöra likhet och relevans mellan två textsträngar.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-begäran och svar med slutpunkt
När du publicerar kunskapsbasen skapar tjänsten en REST-baserad HTTP-slutpunkt som kan integreras i ditt program, vanligtvis en chattrobot.

### <a name="the-user-query-request-to-generate-an-answer"></a>Begäran om användarfråga för att generera ett svar

En användarfråga är den fråga som slutanvändaren ställer `How do I add a collaborator to my app?`till kunskapsbasen, till exempel . Frågan är ofta i ett naturligt språkformat eller några nyckelord `help with collaborators`som representerar frågan, till exempel . Frågan skickas till din kunskapsbas från en HTTP-begäran i klientprogrammet.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Du styr svaret genom att ange egenskaper som [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)och [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Använd [konversationskontext](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) med [funktioner för flera svängar](../how-to/multiturn-conversation.md) för att hålla konversationen igång för att förfina frågorna och svaren, för att hitta rätt och slutgiltigt svar.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Svaret från ett samtal för att generera ett svar

HTTP-svaret är svaret som hämtas från kunskapsbasen, baserat på den bästa matchningen för en viss användarfråga. Svaret innehåller svaret och förutsägelsepoängen. Om du bad om mer än `top` ett toppsvar med egenskapen får du mer än ett toppsvar, var och en med en poäng.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroendepoäng](./confidence-score.md)
