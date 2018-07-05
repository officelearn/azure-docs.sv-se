---
title: Använda metadata i kunskapsbasen tillsammans med GenerateAnswer-API | Microsoft Docs
description: Med metadata med GenerateAnswer API
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 8ad51edde1fc90329e908f8150aadcbf4f401126
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448228"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Med hjälp av metadata och GenerateAnswer-API

QnA Maker kan du lägga till metadata i form av nyckel/värde-par för din fråga/svar-uppsättningar. Den här informationen kan användas på olika sätt, till exempel filtrera resultaten på användarförfrågningar, öka vissa resultat och lagra ytterligare information som kan användas i Uppföljnings konversationer. Mer information finns i [kunskapsbas](../Concepts/knowledge-base.md).

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
    - **Slutpunkt för QnAMaker** (sträng): värdnamnet för den slutpunkt som distribuerats i din Azure-prenumeration.
- **Begärandehuvuden**
    - **Content-Type** (sträng): medietyp i texten som skickas till API: et.
    - **Auktorisering** (sträng): din slutpunktsnyckeln (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Brödtext i begäran**
    - **fråga** (sträng): en användare-fråga som ska frågas mot kunskapsbasen.
    - **översta** (valfritt, heltal): antalet rankad resultat som ska ingå i utdata. Standardvärdet är 1.
    - **användar-ID** (valfri, sträng): ett unikt ID för att identifiera användaren. Detta ID kommer att läggas till i chattloggarna.
    - **strictFilters** (valfri, sträng): om anges talar om för QnA Maker att returnera endast de svar som har angivna metadata. Mer information finns nedan.
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
        - **poäng**: en rangordning mellan 0 och 100.
        - **frågor**: frågor som anges av användaren.
        - **källan**: namnet på källan svaret har extraherats, eller sparats i knowledge base.
        - **metadata**: metadata som associeras med svaret.
            - namn: namn på Metadata. (string, maximal längd: 100, krävs)
            - värde: värde. (string, maximal längd: 100, krävs)
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

Överväg att den under vanliga frågor om data för restauranger i Hyderabad. Lägga till metadata i kunskapsbasen genom att klicka på kugghjulsikonen.

![lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrera resultaten med strictFilters

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

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
