---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: db866da43310f5407ce4daae1cade2c7512b91ea
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369288"
---
Använd Language Understanding (LUIS) förutsägelse klient bibliotek för python för att:

* Hämta förutsägelse per plats
* Hämta förutsägelse efter version

[Referens dokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis)  |  [Förutsägelse körnings paket (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py)

## <a name="prerequisites"></a>Krav

* Language Understanding (LUIS) Portal konto – [skapa ett kostnads fritt](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Ett LUIS app-ID – Använd ID: t för den offentliga IoT-appen `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="setting-up"></a>Konfigurera

### <a name="get-your-language-understanding-luis-runtime-key"></a>Hämta din Language Understanding (LUIS) körnings nyckel

Hämta din [körnings nyckel](../luis-how-to-azure-subscription.md) genom att skapa en Luis runtime-resurs. Behåll din nyckel och slut punkten för nyckeln för nästa steg.

### <a name="create-a-new-python-file"></a>Skapa en ny python-fil

Skapa en ny python-fil i önskat redigerings program eller IDE, med namnet `prediction_quickstart.py` .

### <a name="install-the-sdk"></a>Installera SDK:n

I program katalogen installerar du klient biblioteket Language Understanding (LUIS) förutsägelse körning för python med följande kommando:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objekt modell

Den Language Understanding (LUIS) förutsägelse runtime-klienten är ett [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) -objekt som autentiserar till Azure, som innehåller din resurs nyckel.

När klienten har skapats använder du den här klienten för att få åtkomst till funktioner, inklusive:

* Förutsägelse per [mellanlagrings-eller produktions plats](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Förutsägelse efter [version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket Language Understanding (LUIS) förutsägelse körning för python:

* [Förutsägelse per plats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du `prediction_quickstart.py` filen i önskat redigerings program eller IDE. Lägg till följande beroenden:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för din egen obligatoriska LUIS-information: din förutsägelse nyckel och slut punkt.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. Skapa en variabel för app-ID: t som angetts till den offentliga IoT-appen **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Skapa en variabel för att ange den `production` publicerade platsen.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. Skapa ett inloggnings objekt med din nyckel och Använd slut punkten för att skapa ett [LUISRuntimeClientConfiguration](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python) -objekt.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Hämta förutsägelse från körning

Lägg till följande metod för att skapa begäran till förutsägelse körning.

Användaren uttryck är en del av [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) -objektet.

Metoden **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** behöver flera parametrar, till exempel App-ID, plats namn och objektet förutsägelse begär Ande för att uppfylla begäran. De andra alternativen, till exempel verbose, Visa alla avsikter och loggen är valfria. Begäran returnerar ett [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) -objekt.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Huvud kod för förutsägelsen

Använd följande huvudsakliga metod för att koppla ihop variablerna och metoderna för att hämta förutsägelsen.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python prediction_quickstart.py` kommandot från program katalogen.

```console
python prediction_quickstart.py
```

Snabb starts konsolen visar utdata:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser kan du rensa arbetet från den här snabb starten genom att ta bort filen och dess under kataloger.
