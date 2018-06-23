---
title: Etiketten entiteter automatiskt med en lista över entitet med Nodejs | Microsoft Docs
description: Lär dig hur du lägger till en entitet listan för att hjälpa THOMAS etikett variationer av ord eller fraser.
services: cognitive-services
author: v-geberr
titleSuffix: Azure
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e8558ecf4a64dbccef6e6367c1447bdcdb005126
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351777"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Använd en entitet i listan för att öka entitet identifiering 
Den här kursen visar hur en [listan entiteten](luis-concept-entity-types.md) att öka entitet identifiering. Lista över enheter behöver inte är märkt som de är en exakt matchning av villkoren.  

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Skapa en entitet i listan 
* Lägg till normaliserade värden och synonymer
* Validera förbättrad enhetens identifiering

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * Senaste [Node.js](https://nodejs.org)
> * [HomeAutomation THOMAS app](luis-get-started-create-app.md). Om du inte har appen Start Automation skapas, skapa en ny app och Lägg till domänen färdiga **HomeAutomation**. Träna och publicera appen. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (om frågar många gånger), app-ID, versions-ID och [region](luis-reference-regions.md) för THOMAS appen.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här kursen är tillgängligt på den [THOMAS prover github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Använda HomeAutomation app
Appen HomeAutomation ger kontroll över enheter, till exempel ljus, underhållning system och miljö styr som uppvärmning och kylning. Dessa system har flera olika namn som kan innehålla tillverkare namn, smeknamn, förkortningar och slang. 

Ett system som har många namn över olika kulturer och demografi är termostat. En termostat kan styra både kylning och systemet för ett hus eller en byggnad.

Helst följande utterances ska matcha fördefinierade entiteten **HomeAutomation.Device**:

|#|utterance|person|poäng|
|--|--|--|--|
|1|Aktivera ac|HomeAutomation.Device - ”ac”|0.8748562|
|2|Höj värme|HomeAutomation.Device - ”termiska”|0.784990132|
|3|gör det där|||

De två första utterances mappas till olika enheter. Den tredje utterance ”gör det kalla”, mappas inte till en enhet, men i stället begär ett resultat. THOMAS vet inte att termen ”kalla” innebär termostat är den begärda enheten. Vi rekommenderar ska THOMAS matcha alla dessa utterances på samma enhet. 

## <a name="use-a-list-entity"></a>Använda en entitet i listan
Entiteten HomeAutomation.Device är perfekt för ett litet antal enheter eller med några variationer av namnen. För en kontorsbyggnad eller plats växa enhetsnamnen mer användbarhet HomeAutomation.Device entiteten. 

En **listan entiteten** är ett bra alternativ för det här scenariot eftersom uppsättning villkor för en enhet i en byggnad eller plats är en känd uppsättning, även om det är en stor mängd. Med hjälp av en enhet i listan, THOMAS får alla möjliga värde i uppsättningen för termostat och matcha den ned bara enskild enhet ”termostat”. 

Den här självstudiekursen kommer att skapa en entitet lista med termostat. Alternativa namn för en termostat i den här kursen är: 

|alternativa namn för termostat|
|--|
| AC |
| konto|
| en c|
|värmare|
|hot|
|högre temperaturer|
|kalla|
|kalla|

Om THOMAS måste fastställa ett nytt alternativ ofta en [frasen listan](luis-concept-feature.md#how-to-use-phrase-lists) är ett bättre svar.

## <a name="create-a-list-entity"></a>Skapa en entitet i listan
Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Använder du följande kommando för att installera NPM-beroenden och köra kod för att skapa entiteten lista:

```Javascript
npm install && node add-entity-list.js
```

Utdata från kör är ID för entiteten lista:

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>Träna modellen
Träna THOMAS för den nya listan att påverka resultatet av frågan. Utbildning är en process i två delar av utbildning, sedan kontrollerar statusen om utbildningen görs. En app med många modeller kan ta en stund att träna. Följande kod tränar appen och väntar tills utbildningen lyckas. Vänta och återförsöksstrategi används för att undvika 429 ”för många begäranden” fel. 

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Använd följande kommando för att köra kod för att träna appen:

```Javascript
node train.js
```

Utdata för körningen är statusen för varje iteration av utbildning av THOMAS-modeller. Följande körning krävs endast en kontroll av utbildning:

```Javascript
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
Publicera så listan enheten är tillgänglig från slutpunkten.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utöver kvotgränsen.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Använd följande kommando för att köra kod för att fråga appen:

```Javascript
node publish.js
```

Följande utdata innehåller slutpunkts-url för frågor. Verklig JSON-resultaten kan inkludera verkliga appID. 

```JSON
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

## <a name="query-the-app"></a>Frågan appen 
Fråga appen från slutpunkten att bevisa att entiteten listan hjälper THOMAS avgör typ av enhet.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utöver kvotgränsen.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Använd följande kommando för att köra och fråga appen:

```Javascript
node train.js
```

Utdata är resultatet av frågan. Eftersom kod som lagts till i **utförlig** namn/värde-par till frågesträngen utdata innehåller alla avsikter och deras resultat:

```JSON
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

Enheten för **termostat** identifieras med en resultatet indatavärdena fråga på ”Stäng av termiska”. Eftersom den ursprungliga HomeAutomation.Device entiteten är fortfarande i appen, ser du även dess resultat. 

Försök de andra två utterances för att se att de returneras även som en termostat. 

|#|utterance|entitet|typ|värde|
|--|--|--|--|--|
|1|Aktivera ac| AC | DevicesList | Termostat|
|2|Höj värme|termisk| DevicesList |Termostat|
|3|gör det där|kalla|DevicesList|Termostat|

## <a name="next-steps"></a>Nästa steg

Du kan skapa en annan entitet i listan om du vill expandera enheten platser rum, golv eller byggnader. 
