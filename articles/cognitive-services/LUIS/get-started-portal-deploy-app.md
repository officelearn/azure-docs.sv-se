---
title: 'Snabb start: Distribuera en app med LUIS-portalen'
titleSuffix: Azure Cognitive Services
description: Den här snabb starten visar hur du distribuerar en app genom att skapa en förutsägelse slut punkts resurs, tilldela resursen, utbildningen och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 65af2caa2272549b5ad562ff3c38b90e3ea43fd5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278548"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snabb start: Distribuera en app i LUIS-portalen

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

När din LUIS-app är redo att returnera uttryck förutsägelser till ett klient program (till exempel en chatt-robot) måste du distribuera appen till förutsägelse slut punkten.

I den här snabb starten lär du dig att distribuera ett program. Du skapar en förutsägelse slut punkt resurs, tilldelar resursen till appen, tränar appen och publicerar appen.

## <a name="prerequisites"></a>Krav

* Skaffa en [Azure-prenumeration](https://azure.microsoft.com/free).
* Slutför den [föregående Portal snabb](get-started-portal-build-app.md) starten eller [Ladda ned och importera appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* [Migrera till en Azure-resurs](luis-migration-authoring.md)om du har appar som är i förväg inaktuella Azure-resurs-autentisering. Vissa Portal sidor ser annorlunda ut när e-postautentiseringen är aktiv.

## <a name="create-the-endpoint-resource"></a>Skapa slut punkts resursen

Du skapar en förutsägelse slut punkts resurs i Azure Portal. Den här resursen bör endast användas för slut punkts förutsägelse frågor. Använd inte den här resursen för att redigera ändringar i appen.

1. Logga in och skapa en resurs i [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Konfigurera prenumerationen med följande inställningar:

   |Inställning|Värde|Syfte|
   |--|--|--|
   |Namn|`my-luis-resource`|Namnet på Azure-resursen. Du behöver det här namnet när du tilldelar resursen till appen i LUIS-portalen.|
   |Prenumeration|Din prenumeration|Välj en av prenumerationerna som är kopplade till ditt konto.|
   |Resursgrupp|`my-resource-group`|Skapa en ny resurs grupp för alla dina kognitiva tjänst resurser. När du är klar med resurserna kan du ta bort resurs gruppen för att rensa prenumerationen. |
   |Redigerings plats|**USA, västra**|Azure-regionen för redigering.|
   |Redigera pris nivå|**F0**|Standard pris nivån för redigering.|
   |Körnings plats|**USA, västra**|Azure-regionen för förutsägelse slut punkts frågor.|
   |Pris nivå för körning|**S0**|Den här pris nivån ger till gång till en webbplats med hög trafik.|
   | | | |


   ![Val av Azure-API](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Välj **skapa** för att skapa Azure-resursen.

   I nästa avsnitt får du lära dig hur du ansluter den här nya resursen till en LUIS-app på LUIS-portalen.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Tilldela resurs nyckeln till LUIS-appen i LUIS-portalen

Varje gång du skapar en ny resurs för LUIS måste du tilldela resursen till LUIS-appen. När du har tilldelat dig behöver du inte göra detta steg igen om du inte skapar en ny resurs. Du kan skapa en ny resurs för att expandera regionerna i appen eller för att stödja ett större antal förutsägelse frågor.

1. Logga in på [Luis-portalen](https://preview.luis.ai) och välj **myEnglishApp** -appen från listan appar.

1. Välj **Hantera** på menyn längst upp till höger och välj sedan **Azure-resurser**.

1. Om du vill lägga till LUIS väljer du **Lägg till förutsägelse resurs**.

    ![Om du vill lägga till LUIS förutsägelse resurs väljer du Lägg till förutsägelse resurs](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Välj klient, prenumeration och resurs namn. Välj **tilldela resurs**.

   ![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Slutför samma steg för att lägga till redigerings nyckeln till din app.

1. Hitta den nya raden i tabellen för den nya förutsägelse resursen och kopiera slut punkts-URL: en. Den är korrekt konstruerad för att göra en `HTTP GET`-begäran till LUIS API-slutpunkten för en förutsägelse.

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publicera appen till förutsägelse slut punkten

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Förutsägelse slut punkts förfrågan

I för hands versionen av portalen är `query=` i slutet av URL: en där användarens uttryck läggs till i GET-begäran. Efter `query=`anger du samma användar-uttryck som användes i slutet av föregående snabb start:

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

Om du vill se samma informations nivå i test fönstret måste du publicera appen. När appen har publicerats väljer du **Jämför med publicerad** i test fönstret. Använd **Visa JSON-vy** i det publicerade test fönstret för att se samma JSON som i föregående steg. På så sätt kan du jämföra ändringar i den aktuella appen som du arbetar med med en app som publiceras till slut punkten.

[![jämför den aktuella redigeringen jämfört med den publicerade versionen av appen](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starten väljer du **Mina appar** på den översta navigerings menyn. Markera kryss rutan för appen i listan och välj sedan **ta bort** från verktygsfältet kontext ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera vanliga avsikter och entiteter](luis-tutorial-prebuilt-intents-entities.md)
