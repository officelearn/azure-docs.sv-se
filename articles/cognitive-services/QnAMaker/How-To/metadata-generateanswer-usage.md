---
title: Använda metadata i kunskapsbasen tillsammans med GenerateAnswer API | Microsoft Docs
description: Använda metadata med GenerateAnswer API
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356041"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Med hjälp av metadata och GenerateAnswer-API

Frågor och svar om Maker låter dig lägga till metadata, i form av nyckel/värde-par fråga/svar-uppsättningar. Den här informationen kan användas på olika sätt, till exempel filtrerar resultaten av användarfrågor förstärkning vissa resultat och lagra ytterligare information som kan användas i uppföljning konversationer. Mer information finns i [Knowledge base](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Frågor och svar om entiteten

Först är det viktigt att förstå hur frågor och svar om Maker lagrar fråga/svar-data. Följande bild visar en frågor och svar om entitet:

![Frågor och svar om entiteten](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje frågor och svar om entiteten har ett unikt och beständiga-ID. ID som kan användas för att göra uppdateringar för en viss frågor och svar om entitet.

## <a name="generateanswer-api"></a>GenerateAnswer API

Du kan använda GenerateAnswer API på Bot eller i programmet för att fråga kunskapsbasen med en fråga för användaren att hämta den som bäst matchningen från fråga/svar-uppsättningar.

### <a name="generateanswer-endpoint"></a>GenerateAnswer slutpunkt

När du publicerar kunskapsbasen, antingen från den [frågor och svar om Maker portal](https://www.qnamaker.ai), eller med hjälp av den [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), du kan hämta information om din GenerateAnswer slutpunkt.

Hämta din information:
1. Logga in på [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. I **min knowledge baser**, klicka på **Visa kod** för kunskapsbasen.
![Min knowledge baser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Hämta information om din GenerateAnswer.

![information om den](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också få din information från den **inställningar** fliken i kunskapsbasen.

### <a name="generateanswer-request"></a>GenerateAnswer begäran

Du kan anropa GenerateAnswer med en HTTP POST-begäran. Exempel som visar hur du anropar GenerateAnswer, finns det [Snabbstart](../quickstarts/csharp.md).

- **URL-begäran**: https://{QnA Maker endpoint} /knowledgebases/ {kunskapsbas ID} / generateAnswer

- **Parametrar för begäran**: 
    - **Kunskapsbasen ID** (sträng): GUID för kunskapsbasen.
    - **QnAMaker endpoint** (sträng): värdnamnet för den slutpunkt som distribuerats i din Azure-prenumeration.
- **Huvuden för begäran**
    - **Content-Type** (sträng): medietyp för brödtext som skickas till API: et.
    - **Auktorisering** (sträng): endpoint-nyckel.
- **Begärandetexten**
    - **fråga** (sträng): en användare fråga ska efterfrågas mot kunskapsbasen.
    - **TOP** (valfritt, heltal): antalet rangordnas resultat som ska ingå i utdata. Standardvärdet är 1.
    - **användar-ID** (valfritt, string): ett unikt ID för att identifiera användaren. Detta ID registreras i chatt-loggarna.
    - **strictFilters** (valfritt, string): Om du anger frågor och svar om Maker att returnera svar som har angivna metadata. Mer information finns nedan.
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

- **Svaret 200** -lyckade anrop returnerar resultatet av frågan. Svaret innehåller följande fält:
    - **svaren** -svar för användarfrågan, sorterade i fallande rangordning poäng.
        - **poäng**: en rangordning poäng på mellan 0 och 100.
        - **frågor**: frågor som anges av användaren.
        - **källan**: namnet på källan svaret har extraherats, eller sparats i knowledge base.
        - **metadata**: metadata som associeras med svaret.
            - namn: Metadatanamn. (sträng, maximal längd: 100, krävs)
            - värde: metadatavärde. (sträng, maximal längd: 100, krävs)
        - **ID**: ett unikt ID för svaret.
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

Överväg att i nedan vanliga frågor om data för hotell i Hyderabad. Lägga till metadata i knowledge base genom att klicka på ikonen växeln.

![Lägg till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrera resultat med strictFilters

Överväg att användaren frågan ”när stöder den här hotell Stäng”? Om avsikten är angiven för restaurang ”Paradise”.

Eftersom resultat krävs endast för restaurang ”Paradise”, kan du ange ett filter i GenerateAnswer anropet på metadata ”restaurang Name”, enligt följande.

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
Svaret på GenerateAnswer innehåller motsvarande metadatainformation för uppsättningen matchade fråga/svar på följande sätt.

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

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
