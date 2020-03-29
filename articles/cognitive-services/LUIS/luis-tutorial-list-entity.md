---
title: Extact textmatchning entiteter - LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till en listentitet för att hjälpa LUIS-etikettvariationer av ett ord eller en fras.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499484"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Använda en listentitet för att öka entitetsidentifiering 
Den här artikeln visar hur [entitet](luis-concept-entity-types.md) används för att öka entitetens identifiering. Listentiteter behöver inte märkas eftersom de är en exakt matchning av termer.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en listentitet 
> * Lägga till normaliserade värden och synonymer
> * Verifiera förbättrad enhetsidentifiering

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * Senaste [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS app](luis-get-started-create-app.md). Om du inte har skapat home automation-appen skapar du en ny app och lägger till den fördefinierade **homeautomationen**för domän . Träna och publicera appen. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (om du frågar många gånger), app-ID, versions-ID och [region](luis-reference-regions.md) för LUIS-appen.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för ett [gratis konto](https://azure.microsoft.com/free/).

All kod i den här artikeln är tillgänglig i [Azure-Samples GitHub-databasen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Använd appen HomeAutomation
HomeAutomation-appen ger dig kontroll över enheter som lampor, underhållningssystem och miljökontroller som uppvärmning och kylning. Dessa system har flera olika namn som kan innehålla tillverkare namn, smeknamn, förkortningar och slang. 

Ett system som har många namn över olika kulturer och demografi är termostaten. En termostat kan styra både kyl- och värmesystem för ett hus eller en byggnad.

Helst bör följande yttranden lösa till den fördefinierade enheten **HomeAutomation.Device:**

|#|Yttrande|enhet som identifierats|poäng|
|--|--|--|--|
|1|slå på ac|HomeAutomation.Device - "ac"|0.8748562|
|2|skruva upp värmen|HomeAutomation.Device - "värme"|0.784990132|
|3|gör det kallare|||

De två första yttrandena mappas till olika enheter. Det tredje uttrycket, "gör det kallare", mappas inte till en enhet utan begär i stället ett resultat. LUIS vet inte att termen "kallare", betyder att termostaten är den begärda enheten. Helst bör LUIS lösa alla dessa yttranden till samma enhet. 

## <a name="use-a-list-entity"></a>Använda en listentitet
Entity homeAutomation.Device är bra för ett litet antal enheter eller med få varianter av namnen. För en kontorsbyggnad eller ett campus växer enhetsnamnen bortom användbarheten för entityen HomeAutomation.Device. 

En **listentitet** är ett bra val för det här scenariot eftersom uppsättningen termer för en enhet i en byggnad eller ett campus är en känd uppsättning, även om det är en stor uppsättning. Genom att använda en lista enhet, LUIS kan ta emot alla möjliga värden i uppsättningen för termostaten, och lösa det ner till bara den enda enheten "termostat". 

Denna artikel kommer att skapa en enhet lista med termostaten. De alternativa namnen för en termostat i den här artikeln är: 

|alternativa namn för termostat|
|--|
| Ac |
| a/c|
| a-c|
|Värmare|
|snabbtangent|
|Varmare|
|Kallt|
|Kallare|

Om LUIS behöver bestämma ett nytt alternativ ofta, då en [fraslista](luis-concept-feature.md#how-to-use-phrase-lists) är ett bättre svar.

## <a name="create-a-list-entity"></a>Skapa en listentitet
Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Använd följande kommando för att installera NPM-beroenden och köra koden för att skapa listentiteten:

```console
npm install && node add-entity-list.js
```

Utdata för körningen är ID för listentiteten:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Träna modellen
Träna LUIS för att den nya listan ska påverka frågeresultaten. Utbildning är en tvådelad utbildningsprocess och kontrollerar sedan status om utbildningen är klar. En app med många modeller kan ta en stund att träna. Följande kod tränar appen väntar sedan tills utbildningen lyckas. Koden använder en strategi för att vänta och försöka för att undvika felet 429 "För många begäranden". 

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Använd följande kommando för att köra koden för att träna appen:

```console
node train.js
```

Utdata för körningen är status för varje iteration av utbildningen av LUIS-modellerna. Följande utförande krävde endast en kontroll av utbildning:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publicera modellen
Publicera så att listentiteten är tillgänglig från slutpunkten.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena för slutpunktsnyckel, appId och region. Du kan använda din authoringKey om du inte planerar att anropa filen utöver kvotgränsen.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Använd följande kommando för att köra koden för att fråga appen:

```console
node publish.js
```

Följande utdata innehåller slutpunkts url för alla frågor. Real JSON resultat skulle omfatta den verkliga appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Fråga appen 
Fråga appen från slutpunkten för att bevisa att listentiteten hjälper LUIS att avgöra enhetstypen.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena för slutpunktsnyckel, appId och region. Du kan använda din authoringKey om du inte planerar att anropa filen utöver kvotgränsen.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Använd följande kommando för att köra koden och fråga appen:

```console
node train.js
```

Utdata är frågeresultatet. Eftersom koden lade till det **utförliga** namnet/värdeparet i frågesträngen innehåller utdata alla avsikter och deras poäng:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Den specifika enheten **för termostat** identifieras med en resultatorienterad fråga om "vrid upp värmen". Eftersom den ursprungliga HomeAutomation.Device-entiteten fortfarande finns i appen kan du också se dess resultat. 

Prova de andra två yttranden för att se att de också returneras som en termostat. 

|#|Yttrande|Enhet|typ|värde|
|--|--|--|--|--|
|1|slå på ac| Ac | EnheterLista | Termostat|
|2|skruva upp värmen|Värme| EnheterLista |Termostat|
|3|gör det kallare|Kallare|EnheterLista|Termostat|

## <a name="next-steps"></a>Nästa steg

Du kan skapa en annan listentitet för att utöka enhetsplatser till rum, våningar eller byggnader. 
