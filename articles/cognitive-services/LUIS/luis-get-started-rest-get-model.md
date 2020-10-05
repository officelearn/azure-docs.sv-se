---
title: 'Snabb start: Hämta modell med REST API: er – LUIS'
titleSuffix: Azure Cognitive Services
description: I den här REST API snabb starten använder du sväng för att lägga till yttranden och träna en modell.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: e5cf3160e6592a48e3a81480480ad8f01cc6af3a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "86206254"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Snabb start: Använd sväng och gå till ändra modell

I den här snabb starten visas hur du lägger till exempel yttranden i en pizza-app och tränar appen. Exempelyttranden är konversationstext från användare som mappas till en avsikt. Genom att tillhandahålla exempelyttranden för avsikter lär du LUIS vilka typer av text från användare som tillhör vilken avsikt.

Genom att följa den här snabb starten utför du tre REST-anrop i följd.

- Först ska du ladda upp en batch med exempel-yttranden som kommer att användas för att träna pizza-appens modell med hjälp av [rest-batchen Lägg till etiketter](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) -anrop.
- Härnäst ska du starta en övningssession för pizza-appen med hjälp av anropet [rest Train Application version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) .
- Slutligen kommer du att få status för utbildningen för pizza-appen med hjälp av [status samtalet rest get-version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) .

[Referens dokumentation](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Förutsättningar

* Ett kostnads fritt [Luis](https://www.luis.ai) -konto.
* En text redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/).
* Kommando rads programmet svänger. Programmet för vändning är redan installerat på macOS, de flesta Linux-distributioner och Windows 10 version 1803 och senare.

    Om du behöver installera svängen kan du hämta svängen från sidan för att [Hämta](https://curl.haxx.se/download.html)en klammer.

## <a name="create-pizza-app"></a>Skapa pizza-app

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Skapa en JSON-fil för att träna pizza-appen

Om du vill skapa en JSON-fil med tre exempel yttranden sparar du följande JSON-data till en fil med namnet `ExampleUtterances.JSON` :

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Exemplet yttranden JSON följer ett speciellt format.

Fältet `text` innehåller texten för exempelyttrandet. Fältet `intentName` måste motsvara namnet på en befintlig avsikt i LUIS-appen. Fältet `entityLabels` är obligatoriskt. Om du inte vill märka ut några entiteter kan du ange en tom matris.

Om matrisen entityLabels inte är tom måste `startCharIndex` och `endCharIndex` märka ut den entitet som anges i fältet `entityName`. Indexet är noll-baserat. Om du börjar eller slutar etiketten på ett blank steg i texten, Miss lyckas API-anropet för att lägga till yttranden.

## <a name="add-example-utterances"></a>Lägga till exempelyttranden

1. Om du vill ladda upp batchen med exempel yttranden kopierar du kommandot till text redigeraren:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Ersätt värdena som börjar med `***YOUR-` med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| URL-slutpunkten för redigering. Exempel: "https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/". Du anger resurs namnet när du skapade resursen.|
    |`***YOUR-APP-ID***`| Ditt LUIS app-ID. |
    |`***YOUR-APP-VERSION***`| LUIS-appens version. För pizza-appen är versions numret "0,1" utan citat tecken.|
    |`***YOUR-AUTHORING-KEY***`|Din redigerings nyckel för 32-tecknen.|

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet hantera på sidan **Azure-resurser** . App-ID: t är tillgängligt i samma hantera-avsnitt på sidan **program inställningar** .

1. Starta en kommando tolk (Windows) eller Terminal (macOS och Linux) och ändra kataloger till samma katalog där du sparade `ExampleUtterances.JSON` filen.

1. Kopiera kommandot vändning från redigeraren och klistra in det i en kommando tolk (Windows) eller Terminal (macOS och Linux). Kör kommandot genom att trycka på RETUR.

    Du bör se följande svar:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Här är utdata som är formaterade för läsbarhet:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Träna pizza-appens modell

1. För att starta en övningssession för pizza-appen kopierar du kommandot till text redigeraren:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Ersätt värdena som börjar med `***YOUR-` med dina egna värden som du gjorde tidigare.

1. Kopiera kommandot vändning från redigeraren och klistra in det i en kommando tolk (Windows) eller Terminal (macOS och Linux). Kör kommandot genom att trycka på RETUR.

    Du bör se följande svar:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Här är utdata som är formaterade för läsbarhet:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Hämta status för utbildning

1. Kopiera det här kommandot till text redigeraren för att få utbildnings status för utbildningen:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Ersätt värdena som börjar med `***YOUR-` med dina egna värden som du gjorde tidigare.

1. Kopiera kommandot vändning från redigeraren och klistra in det i en kommando tolk (Windows) eller Terminal (macOS och Linux). Kör kommandot genom att trycka på RETUR.

    Du bör se följande svar:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Här är utdata som är formaterade för läsbarhet:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort `ExampleUtterances.JSON` filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för en app](luis-concept-best-practices.md)
