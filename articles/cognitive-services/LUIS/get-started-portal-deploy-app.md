---
title: 'Snabb start: Distribuera en app med LUIS-portalen'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar din LUIS-app till förutsägelse slut punkten när appen är redo att returnera uttryck förutsägelser till ett klient program, till exempel en chatt-robot. Den här snabb starten visar hur du distribuerar ett program genom att skapa en förutsägelse slut punkt resurs, tilldela resursen till appen, träna appen och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488718"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snabb start: Distribuera en app i LUIS-portalen

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


När din LUIS-app är redo att returnera uttryck förutsägelser till ett klient program (till exempel en chatt-robot) måste du distribuera appen till förutsägelse slut punkten.

I den här snabb starten lär du dig att distribuera ett program. Du skapar en förutsägelse slut punkt resurs, tilldelar resursen till appen, tränar appen och publicerar appen.

## <a name="prerequisites"></a>Förutsättningar

* Skaffa en [Azure-prenumeration](https://azure.microsoft.com/free).
* Slutför den [föregående Portal snabb](get-started-portal-build-app.md) starten eller [Ladda ned och importera appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Skapa slut punkts resursen

Du skapar en förutsägelse slut punkts resurs i Azure Portal. Den här resursen bör endast användas för slut punkts förutsägelse frågor. Använd inte den här resursen för att redigera ändringar i appen.

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).

1. Välj det gröna **+s** tecknet i den övre vänstra panelen. Sök efter `Cognitive Services` på Marketplace och markera det.

1. Konfigurera prenumerationen med följande inställningar:

   |Inställning|Värde|Syfte|
   |--|--|--|
   |Namn|`my-cognitive-service-resource`|Namnet på Azure-resursen. Du behöver det här namnet när du tilldelar resursen till appen i LUIS-portalen.|
   |Prenumeration|Din prenumeration|Välj en av prenumerationerna som är kopplade till ditt konto.|
   |Plats|**USA, västra**|Azure-regionen för den här resursen.|
   |Prisnivå|**S0**|Standard pris nivån för den här resursen.|
   |Resursgrupp|`my-cognitive-service-resource-group`|Skapa en ny resurs grupp för alla dina kognitiva tjänst resurser. När du är klar med resurserna kan du ta bort resurs gruppen för att rensa prenumerationen. |
   | | | |

   ![Val av Azure-API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Välj **skapa** för att skapa Azure-resursen.

   I nästa avsnitt får du lära dig hur du ansluter den här nya resursen till en LUIS-app på LUIS-portalen.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Tilldela resurs nyckeln till LUIS-appen i LUIS-portalen

Varje gång du skapar en ny resurs för LUIS måste du tilldela resursen till LUIS-appen. När du har tilldelat dig behöver du inte göra detta steg igen om du inte skapar en ny resurs. Du kan skapa en ny resurs för att expandera regionerna i appen eller för att stödja ett större antal förutsägelse frågor.

1. Logga in på [Luis-portalen](https://www.luis.ai) och välj **myEnglishApp** -appen från listan appar.

1. Välj **Hantera** på menyn längst upp till höger och välj sedan **Azure-resurser**.

1. Om du vill lägga till LUIS väljer du **Lägg till förutsägelse resurs**.

    <!-- TBD: get screenshot-->

1. Välj klient, prenumeration och resurs namn. Välj **tilldela resurs**.

   ![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Hitta den nya raden i tabellen och kopiera slut punkts-URL: en. Den är korrekt konstruerad för att göra en `HTTP GET`-begäran till LUIS API-slutpunkten för en förutsägelse.

## <a name="train-and-publish-the-app"></a>Träna och publicera appen

Träna appen när du är redo att testa den. Publicera appen när du vill att den aktuella intränade versionen ska vara tillgänglig för klient program från slut punkts körningen för frågekörning.

1. Om appen är inte tränad väljer du **träna** på menyn i det övre högra hörnet.

1. Välj **publicera** på den översta menyn. Välj produktions plats och publicera.

1. När meddelande fältet visas slutförs publiceringen.

1. På sidan Hantera avdelningens **Azure-resurser** letar du upp listan över tilldelade resurser och motsvarande slut punkts-URL: er.

1. Kopiera exempel frågan till ett webbläsarfönster och Lägg till din användar uttryck som `query` parameter.

## <a name="prediction-endpoint-request"></a>Förutsägelse slut punkts förfrågan

`query=` i slutet av URL: en är kort för **fråga** och är där användarens uttryck läggs till i get-begäran. Efter `query=`anger du samma användar-uttryck som användes i slutet av föregående snabb start:

```Is there a form named hrf-234098```

Se till att frågesträngen innehåller följande par:

* `show-all-intents=true`
* `verbose=true`

Webbläsaren visar svaret:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Om du vill se samma informations nivå i test fönstret måste du publicera appen. När appen har publicerats väljer du **Jämför med publicerad** i test fönstret. Använd **Visa JSON-vy** i det publicerade test fönstret för att se samma JSON som i föregående steg. På så sätt kan du jämföra den aktuella appen som du arbetar med med en app som publiceras till slut punkten.

[![jämför den aktuella redigeringen jämfört med den publicerade versionen av appen](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starten väljer du **Mina appar** på den översta navigerings menyn. Markera kryss rutan för appen i listan och välj sedan **ta bort** från verktygsfältet kontext ovanför listan.

[![ta bort appen från listan Mina appar](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera vanliga avsikter och entiteter](luis-tutorial-prebuilt-intents-entities.md)
