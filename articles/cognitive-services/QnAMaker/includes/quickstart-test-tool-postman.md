---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: dd44d9cb01ff072d89afeb4efc4a59071c621315
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758808"
---
Denna Postmanbaserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas.

## <a name="prerequisites"></a>Krav

* Senaste [**Postman**](https://www.getpostman.com/).
* Du måste ha
    * En [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md)
    * En tränad och publicerad [kunskapsbas med frågor och svar](../Quickstarts/add-question-metadata-portal.md) byggda från snabbstarten är konfigurerad med metadata och Chit-chatt.

> [!NOTE]
> När du är redo att skapa ett svar på en fråga från din kunskapsbas måste du [träna](../Quickstarts/create-publish-knowledge-base.md#save-and-train) och [publicera](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) din kunskapsbas. När din kunskapsbas publiceras visar sidan **publicera** inställningar för HTTP-begäran för att generera ett svar. På fliken **Brevbärare** visas de inställningar som krävs för att generera ett svar.

## <a name="set-up-postman-for-requests"></a>Konfigurera Brevbärare för begäranden

Den här snabbstarten använder samma inställningar för Postman **POST-begäran** och konfigurerar sedan till POST-brödtexten JSON som skickas till tjänsten baserat på vad du försöker fråga efter.

Använd den här proceduren för att konfigurera Postman och sedan läsa varje efterföljande avsnitt för att konfigurera POST-brödtexten JSON.

1. På sidan Inställningar **i** kunskapsbasen väljer du fliken **Postman** för att se den konfiguration som används för att generera ett svar från kunskapsbasen. Kopiera följande information som ska användas i Postman.

    |Namn|Inställning|Syfte och värde|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Det här är HTTP-metoden och vägen för URL:en.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Det här är värden för webbadressen. Sammanfoga värdena Värd och Post för att få den fullständiga generateAnswer-URL:en.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Huvudvärdet för att auktorisera din begäran till Azure. |
    |`Content-type`|`application/json`|Rubrikvärdet för ditt innehåll.|
    ||`{"question":"<Your question>"}`|Brödtexten för POST-begäran som ett JSON-objekt. Det här värdet ändras i varje avsnitt beroende på vad frågan är avsedd att göra.|

1. Öppna Postman och skapa en ny grundläggande **POST-begäran** med dina publicerade kunskapsbasinställningar. I följande avsnitt ändrar du POST-brödtexten JSON för att ändra frågan till din kunskapsbas.

## <a name="use-metadata-to-filter-answer"></a>Använda metadata för att filtrera svar

I en tidigare snabbstart lades metadata till i två QnA-par för att skilja mellan två olika frågor. Lägg till metadata i frågan för att begränsa filtret till bara det relevanta QnA-paret.

1. I Postman ändrar du bara frågan `strictFilters` JSON genom att `service:qna_maker`lägga till egenskapen med namn/värdeparet i . Kroppen JSON bör vara:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Frågan är bara ett `size`enda ord, som kan returnera någon av de två frågorna och svarsuppsättningarna. Matrisen `strictFilters` talar om för svaret `qna_maker` att minska till bara svaren.

1. Svaret innehåller bara svaret som uppfyller filtervillkoren.

    Följande svar har formaterats för läsbarhet:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Om det finns en fråge- och svarsuppsättning som inte uppfyllde söktermen men som uppfyllde filtret, skulle den inte returneras. I stället returneras det allmänna svaret. `No good match found in KB.`

## <a name="use-debug-query-property"></a>Använd egenskapen felsökningsfråga

Felsökningsinformation hjälper dig att förstå hur det returnerade svaret fastställdes. Även om det är till hjälp, är det inte nödvändigt. Om du vill skapa ett svar `debug` med felsökningsinformation lägger du till egenskapen:

1. I Postman, ändra endast kroppen JSON genom att lägga till fastigheten. `debug` JSON bör vara:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Svaret innehåller relevant information om svaret. I följande JSON-utdata har vissa felsökningsdetaljer ersatts med ellips.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Använd testkunskapsbas

Om du vill få ett svar från testkunskapsbasen använder du kroppsegenskapen. `isTest`

I Postman, ändra endast kroppen JSON genom att lägga till fastigheten. `isTest` JSON bör vara:

```json
{
    'question':'size',
    'isTest': true
}
```

JSON-svaret använder samma schema som den publicerade kunskapsbasfrågan.

> [!NOTE]
> Om testet och de publicerade kunskapsbaserna är exakt desamma kan det fortfarande finnas en liten variation eftersom testindexet delas mellan alla kunskapsbaser i resursen.

## <a name="query-for-a-chit-chat-answer"></a>Fråga efter ett Chit-chat-svar

1. I Postman ändrar du bara brödtexten JSON till ett konversationsslututdrag från användaren. JSON bör vara:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Svaret innehåller poäng och svar.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Eftersom frågan `Thank you` exakt matchar en kommunikationsfråga är QnA Maker helt säker med poängen 100. QnA Maker returnerade också alla relaterade frågor, samt metadataegenskapen som innehåller information om metadatataggen Chit-chat.

## <a name="use-threshold-and-default-answer"></a>Använd tröskelvärde och standardsvar

Du kan begära ett lägsta tröskelvärde för svaret. Om tröskelvärdet inte uppfylls returneras standardsvaret.

1. I Postman ändrar du bara brödtexten JSON till ett konversationsslututdrag från användaren. JSON bör vara:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Kunskapsbasen bör inte hitta det svaret eftersom frågans poäng är 71 %, och i stället returnera det standardsvar du angav när du skapade kunskapsbasen.

    Det returnerade JSON-svaret, inklusive poängen och svaret är:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker returnerade `0`en poäng av , vilket innebär inget förtroende. Det returnerade också standardsvaret.

1. Ändra tröskelvärdet till 60 % och begär frågan igen:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Den returnerade JSON hittade svaret.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```