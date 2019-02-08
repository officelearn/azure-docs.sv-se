---
title: Metadata med GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du lägga till metadata i form av nyckel/värde-par för din fråga/svar-uppsättningar. Den här informationen kan användas för att filtrera resultat på användarförfrågningar och för att lagra ytterligare information som kan användas i Uppföljnings konversationer.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim88
ms.openlocfilehash: 1294b714c217178d53ed69cc886cd89f23620274
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859495"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Med hjälp av metadata och GenerateAnswer-API

QnA Maker kan du lägga till metadata i form av nyckel / värdepar i din fråga/svar-uppsättningar. Den här informationen kan användas för att filtrera resultat på användarförfrågningar och för att lagra ytterligare information som kan användas i Uppföljnings konversationer. Mer information finns i [kunskapsbas](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Frågor och svar om entiteten

Först är det viktigt att förstå hur QnA Maker lagrar fråga/svar-data. Följande bild visar en frågor och svar om entitet:

![Frågor och svar om entiteten](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje entitet för frågor och svar har ett unikt och beständiga-ID. ID: T kan användas för att göra uppdateringar för en viss affärsenhet i frågor och svar.

## <a name="generateanswer-api"></a>GenerateAnswer API

Du kan använda GenerateAnswer-API: er i din robot eller ditt program för att fråga din kunskapsbas med en fråga för användare att få bästa möjliga matchning från fråga/svar-uppsättningar.

### <a name="generateanswer-endpoint"></a>GenerateAnswer slutpunkt

När du publicerar din kunskapsbas, antingen från den [QnA Maker portal](https://www.qnamaker.ai), eller med hjälp av den [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), du kan hämta information om din GenerateAnswer slutpunkt.

Hämta information om din slutpunkt:
1. Logga in på [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. I **min kunskapsbaser**, klicka på **Visa kod** för kunskapsbasen.
![Min kunskapsbaser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Får tillgång till dina GenerateAnswer slutpunkt.

![information om slutpunkten](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också få din slutpunktsinformation från den **inställningar** fliken i kunskapsbasen.

### <a name="generateanswer-request"></a>GenerateAnswer begäran

Du kan anropa GenerateAnswer med en HTTP POST-begäran. Exempel som visar hur du anropar GenerateAnswer, finns det [snabbstarter](../quickstarts/csharp.md).

- **URL för begäran**: https://{QnA Maker endpoint} /knowledgebases/ {kunskapsbas-ID} / generateAnswer

- **Begäranparametrar**: 
    - **Kunskapsbas-ID** (sträng): GUID för kunskapsbasen.
    - **Slutpunkt för QnAMaker** (sträng): Värdnamnet för den slutpunkt som distribuerats i din Azure-prenumeration.
- **Begärandehuvuden**
    - **Content-Type** (sträng): Medietyp i texten som skickas till API: et.
    - **Auktorisering** (sträng): Din slutpunktsnyckeln (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Brödtext i begäran**
    - **fråga** (sträng): En användare fråga ska efterfrågas mot kunskapsbasen.
    - **översta** (valfritt, heltal): Antal översta resultat ska ingå i utdata. Standardvärdet är 1.
    - **användar-ID** (valfri, sträng): Ett unikt ID för att identifiera användaren. Detta ID kommer att läggas till i chattloggarna.
    - **strictFilters** (valfri, sträng): Om anges talar du om QnA Maker att returnera endast de svar som har angivna metadata. Mer information finns nedan.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer svar

- **Svaret 200** -ett genomfört anrop returnerar resultatet av frågan. Svaret innehåller följande fält:
    - **svar** -svar för användarfråga, sorterade i fallande rangordning poäng.
        - **poäng**: En rangordning mellan 0 och 100.
        - **frågor**: Frågor som anges av användaren.
        - **Svar**: Svaret på frågan.
        - **källan**: Namnet på källan svaret har extraherats, eller sparats i knowledge base.
        - **metadata**: Metadata som associeras med svaret.
            - Namn: Namn på metadata. (string, maximal längd: 100 krävs)
            - Värde: Metadata-värde. (string, maximal längd: 100 krävs)
        - **ID**: Ett unikt ID för svaret.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Metadata-exempel

Överväg att den nedanför data för vanliga frågor och svar. Lägga till metadata i kunskapsbasen genom att klicka på ikonen för metadata.

![lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultaten med strictFilters för metadatataggarna

Överväg att användaren frågan ”när fungerar den här hotell Stäng”? Om avsikten är angiven för restaurang ”Paradise”.

Eftersom det behövs endast för restaurang ”Paradise” resultat, kan du ange ett filter i GenerateAnswer anropet om metadata som ”restaurang-Name”, enligt följande.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Behåll kontext
Svaret på GenerateAnswer innehåller matchande fråga/svar-uppsättningen motsvarande metadata-information på följande sätt.

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

Den här informationen kan användas för att registrera kontexten för den tidigare konversationen för användning i senare konversationer. 

## <a name="next-steps"></a>Nästa steg

Publiceringssidan innehåller även information om du vill generera ett svar med [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) och [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
