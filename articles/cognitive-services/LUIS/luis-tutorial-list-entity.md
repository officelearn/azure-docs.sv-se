---
title: Extrakt text matchar entiteter
description: Lär dig hur du lägger till en lista över entitet för att hjälpa LUIS etikett variationer av ett ord eller fraser.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: diberry
ms.openlocfilehash: 4cac20cb44c2400e44ff2ca0939b1537f0b20734
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727190"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Använd en entitet i listan för att öka entitet identifiering 
Den här självstudien visar hur du använder en [lista entitet](luis-concept-entity-types.md) att öka entitet identifiering. Lista enheter behöver inte märkta som de är en exakt matchning av villkor.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
* Skapa en entitet i listan 
* Lägg till normaliserade värden och synonymer
* Verifiera förbättrad enhetens identifiering

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * Senaste [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS-app](luis-get-started-create-app.md). Om du inte har appen Start Automation skapas, skapar en ny app och lägger till fördefinierade domänen **HomeAutomation**. Träna och publicera appen. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (om frågor ofta), app-ID, versions-ID och [region](luis-reference-regions.md) för LUIS-app.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här självstudien är tillgänglig på den [Azure-Samples GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Använda HomeAutomation appar
App HomeAutomation ger kontroll över enheter, till exempel lamporna, underhållning system och miljö som styr, till exempel uppvärmning och kylning. De här systemen har flera olika namn som kan inkludera tillverkare, smeknamn, förkortningar och slang. 

Ett system som har många namn över olika kulturer och demografi är termostat. En termostat kan styra både kylning och systemet för ett house eller en byggnad.

Vi rekommenderar följande yttranden bör matchas mot fördefinierade entiteten **HomeAutomation.Device**:

|#|Uttryck|person|poäng|
|--|--|--|--|
|1|Aktivera ac|HomeAutomation.Device - ”ac”|0.8748562|
|2|Höj termiska|HomeAutomation.Device - ”termisk”|0.784990132|
|3|gör det där|||

De två första yttranden mappas till olika enheter. Den tredje uttryck ”gör den kalla”, mappas inte till en enhet, men i stället begär ett resultat. LUIS vet inte att termen ”kalla” innebär termostat är den begärda enheten. LUIS ska helst matcha alla dessa yttranden på samma enhet. 

## <a name="use-a-list-entity"></a>Använda en entitet i listan
Entiteten HomeAutomation.Device är perfekt för ett litet antal enheter eller med några varianter av namnen. För en kontorsbyggnad eller campus växa enhetsnamn bortom led i entiteten HomeAutomation.Device. 

En **lista entitet** är ett bra val för det här scenariot eftersom uppsättningen med villkor för en enhet i en byggnad eller campus är en känd uppsättning, även om det är en stor uppsättning. Med hjälp av en entitet i listan, LUIS får alla möjliga värde i uppsättningen för termostat och matcha den till bara enkel enhet ”termostat”. 

Den här självstudiekursen kommer att skapa en Entitetslista med termostat. Alternativa namn för en termostat i den här självstudien är: 

|alternativa namn för termostat|
|--|
| AC |
| konto|
| en c|
|heater|
|hot|
|högre temperaturer|
|Kall|
|kalla|

Om LUIS behöver att fastställa ett nytt alternativ ofta en [frasen lista](luis-concept-feature.md#how-to-use-phrase-lists) finns ett bättre svar.

## <a name="create-a-list-entity"></a>Skapa en entitet i listan
Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Använder du följande kommando för att installera de NPM-beroendena och köra kod för att skapa entiteten lista:

```console
npm install && node add-entity-list.js
```

Utdata från körningen är ID för entiteten lista:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Träna modellen
Träna LUIS för att den nya listan att påverka resultatet av frågan. Utbildning är en process i två delar av utbildning, sedan kontrollerar status för om du gör det utbildningen. En app med många modeller kan ta en stund åt att träna. Följande kod träna appen och väntar tills utbildningen har genomförts. Vänta och återförsöksstrategi används för att undvika 429 ”för många begäranden” fel. 

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena authoringKey, appId, versionId och region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Använd följande kommando för att köra koden för att träna appen:

```console
node train.js
```

Utdata från körningen är statusen för varje iteration av utbildning av LUIS-modeller. Följande körning krävs bara en kontroll av utbildning:

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
Publicera så att listan enheten är tillgänglig från slutpunkten.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utanför din kvotgräns.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Använd följande kommando för att köra koden för att fråga efter appen:

```console
node publish.js
```

Följande utdata innehåller slutpunkts-url för frågor. Verkliga JSON-resultat omfattar verkliga appID. 

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
Fråga appen från slutpunkt för att bevisa att entiteten lista hjälper LUIS fastställa typ av enhet.

Skapa en Node.js-fil och kopiera följande kod till den. Ändra värdena endpointKey, appId och region. Du kan använda din authoringKey om du inte planerar att anropa den här filen utanför din kvotgräns.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Använd följande kommando för att köra koden och fråga efter appen:

```console
node train.js
```

Utdata är resultatet av frågan. Eftersom koden har lagts till i **utförlig** namn/värde-par i frågesträngen, utdata innehåller alla avsikter och deras resultat:

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

Den specifika enheten **termostat** identifieras med en fråga med resultatet inriktad på ”Stäng in termiska”. Eftersom den ursprungliga HomeAutomation.Device entiteten är fortfarande i appen, kan du se resultaten samt. 

Prova de andra två yttranden om du vill se att de returneras även som en termostat. 

|#|Uttryck|entitet|typ|värde|
|--|--|--|--|--|
|1|Aktivera ac| AC | DevicesList | Termostat|
|2|Höj termiska|termisk| DevicesList |Termostat|
|3|gör det där|kalla|DevicesList|Termostat|

## <a name="next-steps"></a>Nästa steg

Du kan skapa en annan lista entitet för att expandera enheten platser rum, golv eller byggnader. 
