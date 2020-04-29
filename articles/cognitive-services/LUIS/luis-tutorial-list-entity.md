---
title: Extact text matcha entiteter-LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till en lista entitet som hjälper dig att LUIS etikett variationer i ett ord eller en fras.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73499484"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Använd en lista entitet för att öka identifieringen av entiteter 
Den här artikeln visar hur du använder en [list-entitet](luis-concept-entity-types.md) för att öka identifieringen av entiteter. List enheter behöver inte märkas eftersom de är en exakt matchning av villkoren.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en lista entitet 
> * Lägg till normaliserade värden och synonymer
> * Verifiera förbättrad enhets identifiering

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * Senaste [Node. js](https://nodejs.org)
> * [HOMEAUTOMATION Luis-app](luis-get-started-create-app.md). Om du inte har skapat appen Home Automation skapar du en ny app och lägger till den färdiga domänen **HomeAutomation**. Träna och publicera appen. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (om frågor många gånger), app-ID, versions-ID och [region](luis-reference-regions.md) för Luis-appen.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/).

All kod i den här artikeln är tillgänglig i [GitHub-lagringsplatsen Azure-samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Använda HomeAutomation-app
HomeAutomation-appen ger dig kontroll över enheter som lampor, underhållnings system och miljö kontroller som uppvärmning och kylning. Dessa system har flera olika namn som kan innehålla namn på tillverkare, smek namn, akronymer och slang. 

Ett system som har många namn över olika kulturer och demografiska är termostat. En termostat kan styra både kyl-och uppvärmnings system för ett hus eller en byggnad.

Vi rekommenderar att följande yttranden matchar den fördefinierade entiteten **HomeAutomation. enhet**:

|#|uttryck|identifierad entitet|poäng|
|--|--|--|--|
|1|Aktivera AC|HomeAutomation. Device-"AC"|0,8748562|
|2|Aktivera värmen|HomeAutomation. Device-"hetta"|0,784990132|
|3|gör IT-kallaren|||

De två första yttranden mappar till olika enheter. Den tredje uttryck, "gör IT-kallaren", mappar inte till en enhet utan i stället begär ett resultat. LUIS vet inte att termen, "kall", innebär att termostat är den begärda enheten. Vi rekommenderar att LUIS löser alla dessa yttranden till samma enhet. 

## <a name="use-a-list-entity"></a>Använda en lista entitet
Entiteten HomeAutomation. Device är perfekt för ett litet antal enheter eller med några varianter av namnen. För en kontors byggnad eller en campus-enhet växer enhets namnen bortom användbarheten för entiteten HomeAutomation. Device. 

En **list-entitet** är ett bra alternativ för det här scenariot eftersom uppsättningen villkor för en enhet i en byggnad eller campus är en känd uppsättning, även om det är en enorm uppsättning. Genom att använda en List-entitet kan LUIS ta emot eventuella möjliga värden i uppsättningen för termostat och lösa det nedåt till bara den enda enheten "termostat". 

Den här artikeln går till att skapa en enhets lista med termostat. De alternativa namnen för en termostat i den här artikeln är: 

|alternativa namn för termostat|
|--|
| uttag |
| a/c|
| a-c|
|värmare|
|snabbtangent|
|hotter|
|kalla|
|kallare|

Om LUIS måste fastställa ett nytt alternativ ofta är en [fras lista](luis-concept-feature.md#how-to-use-phrase-lists) ett bättre svar.

## <a name="create-a-list-entity"></a>Skapa en lista entitet
Skapa en Node. js-fil och kopiera följande kod till den. Ändra värdena för authoringKey, appId, versionId och region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Använd följande kommando för att installera NPM-beroenden och kör koden för att skapa List-entiteten:

```console
npm install && node add-entity-list.js
```

Utdata från körningen är ID: t för List entiteten:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Träna modellen
Träna LUIS så att den nya listan kan påverka frågeresultaten. Utbildning är en process i två delar och kontrollerar sedan status om utbildningen är klar. En app med många modeller kan ta en stund att träna. Följande kod tågen-appen väntar sedan tills utbildningen har slutförts. I koden används en strategi för vänta och återförsök för att undvika fel meddelandet 429 "för många begär Anden". 

Skapa en Node. js-fil och kopiera följande kod till den. Ändra värdena för authoringKey, appId, versionId och region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Använd följande kommando för att köra koden för att träna appen:

```console
node train.js
```

Utdata från körningen är status för varje iteration av utbildningen för LUIS-modeller. Följande körning krävde bara en kontroll av utbildning:

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
Publicera så att List entiteten är tillgänglig från slut punkten.

Skapa en Node. js-fil och kopiera följande kod till den. Ändra värdena för endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utöver din kvot gräns.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Använd följande kommando för att köra koden för att fråga appen:

```console
node publish.js
```

Följande utdata innehåller slut punkts-URL: en för alla frågor. Verkliga JSON-resultat omfattar det riktiga appID. 

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
Fråga appen från slut punkten för att bevisa att List entiteten hjälper LUIS att fastställa enhets typen.

Skapa en Node. js-fil och kopiera följande kod till den. Ändra värdena för endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utöver din kvot gräns.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Använd följande kommando för att köra koden och fråga appen:

```console
node train.js
```

Resultatet är frågeresultaten. Eftersom koden lade till **verbose** -namn/värde-paret i frågesträngen innehåller utdata alla avsikter och deras resultat:

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

Den speciella enheten för **termostat** identifieras med en resultat orienterad fråga om "Vänd upp värmen". Eftersom den ursprungliga HomeAutomation. Device-entiteten fortfarande finns i appen kan du även se resultatet. 

Prova de andra två yttranden för att se att de också returneras som en termostat. 

|#|uttryck|entitetsrelation|typ|värde|
|--|--|--|--|--|
|1|Aktivera AC| uttag | DevicesList | Termostat|
|2|Aktivera värmen|termisk| DevicesList |Termostat|
|3|gör IT-kallaren|kallare|DevicesList|Termostat|

## <a name="next-steps"></a>Nästa steg

Du kan skapa en annan lista entitet för att expandera enhets platser till rum, golv eller byggnader. 
