---
title: 'Snabbstart: Distribuera en app med LUIS-portalen'
description: Den här snabbstarten visar hur du distribuerar en app genom att skapa en slutpunktsresurs för förutsägelse, tilldela resursen, utbildning och publicera appen.
ms.topic: quickstart
ms.date: 04/06/2020
ms.openlocfilehash: aaf86766c2357c5382b78cd4a35fd4b159e5c0f3
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756304"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snabbstart: Distribuera en app i LUIS-portalen

När LUIS-appen är redo att returnera uttrycksförutsägelser till ett klientprogram (till exempel en chattrobot) måste du distribuera appen till slutpunkten för förutsägelse.

I den här snabbstarten får du lära dig att distribuera ett program. Du skapar en förutsägelseslutpunktsresurs, tilldelar resursen till appen, tränar appen och publicerar appen.

## <a name="prerequisites"></a>Krav

* Skaffa en [Azure-prenumeration](https://azure.microsoft.com/free).
* Slutför [föregående portalsnabbstart](get-started-portal-build-app.md) eller [hämta och importera appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Om du har appar som föregår Azure-resursautentisering [migrerar du till en Azure-resurs](luis-migration-authoring.md). Vissa portalsidor ser annorlunda ut när e-postautentisering är i kraft.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Tilldela resursnyckeln till LUIS-appen i LUIS-portalen

Varje gång du skapar en ny redigerings- eller frågeprognosresurs för LUIS måste du tilldela resursen till LUIS-appen. När den har tilldelats behöver du inte göra det här steget igen om du inte skapar en ny resurs. Du kan skapa en ny resurs för att expandera regionerna i din app eller för att stödja ett större antal förutsägelsefrågor.

1. Logga in på [LUIS-portalen](https://preview.luis.ai) och välj **myEnglishApp-appen** i applistan.

1. Välj **Hantera** i menyn längst upp till höger och välj sedan **Azure Resources**.

1. Om du vill lägga till LUIS väljer du **Lägg till förutsägelseresurs**.

    ![Om du vill lägga till LUIS-förutsägelseresursen väljer du Lägg till förutsägelseresurs](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Välj klient-, prenumerations- och resursnamn. Välj **Tilldela resurs**.

   ![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Gör samma steg för att lägga till redigeringsnyckeln i appen.

1. Leta reda på den nya raden i tabellen för den nya förutsägelseresursen och kopiera slutpunkts-URL:en. Det är korrekt utformad för `HTTP GET` att göra en begäran till LUIS API-slutpunkten för en förutsägelse.

> [!TIP]
> Om du tänker använda Aktiv inlärning för att förbättra LUIS-appen väljer du **Ändra frågeparametrar** och väljer **Spara loggar**. Den här åtgärden ändrar `log=true` exempel-URL:en genom att lägga till frågestringsparametern. Kopiera och använd den ändrade exempelfråge-URL:en när du gör förutsägelsefrågor till slutpunkten för körning.

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publicera appen till slutpunkten förutsägelse

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Begäran om förutsägelseslutpunkt

I förhandsgranskningsportalen är det `query=` i slutet av webbadressen som användarens uttryck läggs till i GET-begäran. Efter `query=`att du har angett samma användaryttrande som användes i slutet av föregående snabbstart:

```Is there a form named hrf-234098```

Kontrollera att frågesträngen innehåller följande par:

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

Om du vill se samma informationsnivå i testfönstret måste du publicera appen. När appen har publicerats väljer du **Jämför med publiceras** i testfönstret. Använd **Visa JSON-vyn** i den publicerade testfönstret om du vill se samma JSON som föregående steg. På så sätt kan du jämföra ändringar med den aktuella appen som du arbetar med med en app som publiceras till slutpunkten.

[![Jämför redigering mot publicerad version av appen för närvarande](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten väljer du **Mina appar** på den övre navigeringsmenyn. Markera appens kryssruta i listan och välj sedan **Ta bort** från kontextverktygsfältet ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera vanliga avsikter och entiteter](luis-tutorial-prebuilt-intents-entities.md)
