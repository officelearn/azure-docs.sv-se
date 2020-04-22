---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: eabec50f57785bde6760db053eb3b12f6f5b6452
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732039"
---
Använd klientbiblioteket Language Understanding (LUIS) för Python för att:

* Få förutsägelse av kortplats
* Få förutsägelse efter version

[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Förutsägelsekörningspaket (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [ ](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Krav

* Portalkonto (Language Understanding) - [Skapa ett gratis](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Ett LUIS-app-ID – använd det offentliga `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IoT-app-ID:et för . Användarfrågan som används i snabbstartskoden är specifik för den appen.

## <a name="setting-up"></a>Inrätta

### <a name="get-your-language-understanding-luis-runtime-key"></a>Få din körtidsnyckel (Language Understanding)

Hämta [din körningsnyckel](../luis-how-to-azure-subscription.md) genom att skapa en LUIS-körningsresurs. Behåll nyckeln och slutpunkten för nyckeln för nästa steg.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Skapa en ny python-fil

Skapa en ny python-fil i önskad `prediction_quickstart.py`redigerare eller IDE med namnet .

### <a name="install-the-sdk"></a>Installera SDK:n

Installera klientbiblioteket för förutsägelsekörning för Python i programkatalogen (LUIS) med följande kommando:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektmodell

LUIS-förutsägelsekörningsklienten (Language Understanding) är ett [LUISRuntimeClient-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) som autentiserar till Azure, som innehåller din resursnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* Förutsägelse genom [mellanlagring eller produktionsplats](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Förutsägelse per [version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket För förutsägelsekörning för Python för språk understanding (Language Understanding) för Python:

* [Förutsägelse efter kortplats](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna `prediction_quickstart.py` filen i önskad redigerare eller IDE från projektkatalogen. Lägg till följande beroenden:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

1. Skapa variabler för din egen nödvändiga LUIS-information:

    Lägg till variabler för att hantera förutsägelsenyckeln som hämtats från en miljövariabel med namnet `LUIS_RUNTIME_KEY`. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE: t eller shell som kör den stängas och laddas om för att komma åt variabeln. Metoderna skapas senare.

    Skapa en variabel för `LUIS_RUNTIME_ENDPOINT`att hålla ditt resursnamn .

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Skapa en variabel för app-ID:t som en miljövariabel med namnet `LUIS_APP_ID`. Ange miljövariabeln till den offentliga **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** IoT-appen . Skapa en variabel `production` för att ange den publicerade platsen.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Skapa ett autentiseringsobjekt med nyckeln och använd det med slutpunkten för atthttps://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() skapa ett [LUISRuntimeClientConfiguration]-objekt.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Få förutsägelse från körning

Lägg till följande metod för att skapa begäran i förutsägelsekörningen.

Användaren yttrande är en del av [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) objektet.

Metoden **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** behöver flera parametrar, till exempel app-ID, platsnamnet och objektet för förutsägelsebegäran för att uppfylla begäran. De andra alternativen, till exempel utförlig, visa alla avsikter och logg är valfria. Begäran returnerar ett [PredictionResponse-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Huvudkoden för förutsägelsen

Använd följande huvudmetod för att binda variablerna och metoderna tillsammans för att få förutsägelsen.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Köra appen

Kör programmet med `python prediction_quickstart.py` kommandot från programkatalogen.

```console
python prediction_quickstart.py
```

Snabbstartskonsolen visar utdata:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser rensar du arbetet från den här snabbstarten genom att ta bort filen och dess underkataloger.
