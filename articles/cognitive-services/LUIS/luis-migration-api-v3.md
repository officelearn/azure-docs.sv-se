---
title: V2 till v3 API-migrering
titleSuffix: Azure Cognitive Services
description: 'API: erna för version 3-slutpunkt har ändrats. Använd den här guiden för att lära dig hur du migrerar till API: er för version 3-slutpunkt.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213510"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Förhandsversion: Migrera till API version 3. x för LUIS-appar

API: erna för fråga förutsägelse slut punkt har ändrats. Använd den här guiden för att lära dig hur du migrerar till API: er för version 3-slutpunkt. 

Detta v3-API tillhandahåller följande nya funktioner, bland annat viktiga JSON-begäranden och/eller svars ändringar: 

* [Externa entiteter](#external-entities-passed-in-at-prediction-time)
* [Dynamiska listor](#dynamic-lists-passed-in-at-prediction-time)
* [Inbyggda JSON-ändringar för entitet](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Fråge förutsägelsens slut punkts [förfrågan](#request-changes) och [svar](#response-changes) har betydande ändringar för att stödja de nya funktionerna ovan, inklusive följande:

* [Svars objekt ändringar](#top-level-json-changes)
* [Namn referenser för enhets roll i stället för entitetsnamnet](#entity-role-name-instead-of-entity-name)
* [Egenskaper för att markera entiteter i yttranden](#marking-placement-of-entities-in-utterances)

Följande LUIS-funktioner **stöds inte** i v3-API: et:

* Stavningskontroll i Bing v7

[Referens dokumentationen](https://aka.ms/luis-api-v3) är tillgänglig för v3.

## <a name="endpoint-url-changes-by-slot-name"></a>Slut punkts-URL ändras efter plats namn

Formatet på v3-slut punktens HTTP-anrop har ändrats.

|METODSIGNATUR|URL|
|--|--|
|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

Giltiga värden för fack:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Slut punkts-URL ändras efter versions-ID

Om du vill fråga efter version måste du först [publicera via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) med `"directVersionPublish":true`. Fråga slut punkten som refererar till versions-ID: t i stället för plats namnet.


|METODSIGNATUR|URL|
|--|--|
|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Färdiga entiteter med ny JSON

Objektet v3-svars objekt innehåller [fördefinierade entiteter](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Begära ändringar 

### <a name="query-string-parameters"></a>Parametrar för frågesträng

V3-API: et har olika parametrar för frågesträng.

|PARAM-namn|type|Version|Standard|Syfte|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Lagra fråga i logg filen.| 
|`query`|sträng|Endast v3|Ingen standard – det krävs i GET-begäran|**I v2**är uttryck som ska förutsägas i `q` parametern. <br><br>**I v3**skickas funktionerna i `query` -parametern.|
|`show-all-intents`|boolean|Endast v3|false|Returnera alla avsikter med motsvarande Poäng i objektet **förutsägelse. avsikter** . Avsikter returneras som objekt i ett överordnat `intents` objekt. Detta ger program mässig åtkomst utan att behöva hitta avsikten i en matris: `prediction.intents.give`. I v2 returnerades dessa i en matris. |
|`verbose`|boolean|V2 & V3|false|**I v2**returnerades alla förväntade avsikter när värdet är true. Om du behöver alla förutsägande syften använder du v3-parametrarna för `show-all-intents`.<br><br>**I v3**innehåller den här parametern endast information om entitetens metadata för entitet förutsägelse.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Frågans JSON-brödtext för `POST` begäran

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Egenskap|type|Version|Standard|Syfte|
|--|--|--|--|--|
|`dynamicLists`|array|Endast v3|Krävs inte.|Med [dynamiska listor](#dynamic-lists-passed-in-at-prediction-time) kan du utöka en befintlig utbildad och publicerad List-entitet, redan i Luis-appen.|
|`externalEntities`|array|Endast v3|Krävs inte.|[Externa entiteter](#external-entities-passed-in-at-prediction-time) ger din Luis-app möjlighet att identifiera och märka enheter under körning, som kan användas som funktioner till befintliga entiteter. |
|`options.datetimeReference`|sträng|Endast v3|Ingen standard|Används för att fastställa [datetimeV2 offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formatet för datetimeReference är [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Endast v3|false|Anger om användarens [externa entitet (med samma namn som befintlig entitet)](#override-existing-model-predictions) används eller om den befintliga entiteten i modellen används för förutsägelse. |
|`query`|sträng|Endast v3|Obligatoriskt.|**I v2**är uttryck som ska förutsägas i `q` parametern. <br><br>**I v3**skickas funktionerna i `query` -parametern.|



## <a name="response-changes"></a>Svars ändringar

JSON-svaret har ändrats för att ge bättre programmerings åtkomst till de data som används oftast. 

### <a name="top-level-json-changes"></a>JSON-ändringar på högsta nivån

De översta JSON-egenskaperna för v2 är, `verbose` när har angetts till true, vilket returnerar alla intentor och deras resultat `intents` i egenskapen:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De största JSON-egenskaperna för v3 är:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

`intents` Objektet är en osorterad lista. Anta inte att det första barnet i `intents` motsvarar. `topIntent` Använd i stället `topIntent` värdet för att hitta poängen:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Ändringarna i svars-JSON-schemat tillåter:

* Ta bort distinktionen mellan ursprunglig `query`uttryck, och returnerade `prediction`förutsägelser.
* Enklare programmerings åtkomst till förväntade data. I stället för att räkna upp genom en matris i v2 kan du komma åt värden efter **namn** för både avsikter och entiteter. För förväntade enhets roller returneras roll namnet eftersom det är unikt i hela appen.
* Data typer, om de fastställs, är uppfyllda. Numeriska värden returneras inte längre som strängar.
* Skillnaden mellan den första prioriterade förutsägelse informationen och ytterligare metadata som returneras i `$instance` objektet. 

### <a name="access-instance-for-entity-metadata"></a>Åtkomst `$instance` för entitets-metadata

Om du behöver entitetens metadata måste frågesträngen använda `verbose=true` flaggan och svaret innehåller metadata `$instance` i objektet. Exempel visas i JSON-svaren i följande avsnitt.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Varje förväntad entitet representeras som en matris

`prediction.entities.<entity-name>` Objektet innehåller en matris eftersom varje entitet kan förutsägas mer än en gång i uttryck. 

### <a name="list-entity-prediction-changes"></a>Visa lista över enhets förutsägelse ändringar

JSON för en List enhets förutsägelse har ändrats till att vara en matris med matriser:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Varje inre matris motsvarar text inuti uttryck. Det inre objektet är en matris eftersom samma text kan visas i fler än en under lista i en List-entitet. 

Vid mappning mellan `entities` objektet `$instance` och objektet bevaras objekt ordningen för List entiteten förutsägelser.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Enhets roll namn i stället för entitetsnamnet 

I v2 `entities` returnerade matrisen alla förväntade entiteter med entitetens namn som unik identifierare. I v3, om entiteten använder roller och förutsägelsen för en Entity-roll, är den primära identifieraren roll namnet. Detta är möjligt eftersom entiteternas roll namn måste vara unika i hela appen, inklusive andra modell namn (avsikt, entitet).

I följande exempel: Överväg en uttryck som innehåller texten, `Yellow Bird Lane`. Den här texten är förväntad som en `Location` anpassad entitets roll `Destination`för.

|Uttryck-text|Entitetsnamn|Rollnamn|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

I v2 identifieras entiteten av _entitetsnamnet_ med rollen som en egenskap för objektet:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

I v3 refereras entiteten av entitets _rollen_, om förutsägelsen är för rollen:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

I v3, samma resultat med `verbose` flaggan för att returnera metadata för entiteten:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Externa enheter som överförts vid förutsägelse tiden

Externa entiteter ger din LUIS-app möjlighet att identifiera och märka enheter under körning, som kan användas som funktioner till befintliga entiteter. På så sätt kan du använda egna separata och anpassade enhets Extraherare innan du skickar frågor till din förutsägelse slut punkt. Eftersom detta görs på slut punkten för frågans förutsägelse behöver du inte träna och publicera din modell.

Klient programmet tillhandahåller sin egen enhets-Extractor genom att hantera enhets matchning och fastställa platsen i uttryck för den matchade entiteten och sedan skicka den informationen med begäran. 

Externa entiteter är mekanismen för att utöka valfri entitetstyp medan de fortfarande används som signaler till andra modeller som roller, sammansatt och andra.

Detta är användbart för en entitet som bara är tillgänglig vid körning av frågans förutsägelse. Exempel på den här typen av data ändrar ständigt data eller specifika per användare. Du kan utöka en LUIS kontakt-entitet med extern information från en användares kontakt lista. 

### <a name="entity-already-exists-in-app"></a>Enheten finns redan i appen

Värdet `entityName` för den externa entiteten som skickades i slut punkts förfrågan efter inläggs meddelande måste redan finnas i den utbildade och publicerade appen när begäran görs. Typen av entitet spelar ingen roll, men alla typer stöds.

### <a name="first-turn-in-conversation"></a>Första inaktive samtal

Överväg en första uttryck i en chatt robot-konversation där en användare anger följande ofullständiga information:

`Send Hazem a new message`

Begäran från chatt-roboten till Luis kan skicka in information i inläggs texten om `Hazem` så att den matchas direkt som en av användarens kontakter.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

I förutsägelse svaret ingår den externa entiteten, med alla andra förväntade entiteter, eftersom den definieras i begäran.  

### <a name="second-turn-in-conversation"></a>Andra aktivering i konversation

Nästa användarens uttryck i Chat-roboten använder en mer vagt-period:

`Send him a calendar reminder for the party.`

I föregående uttryck använder `him` uttryck som en referens till. `Hazem` Chatten i post texten kan mappas `him` till enhet svärdet som extraheras från den första `Hazem`uttryck.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

I förutsägelse svaret ingår den externa entiteten, med alla andra förväntade entiteter, eftersom den definieras i begäran.  

### <a name="override-existing-model-predictions"></a>Åsidosätt befintliga modell förutsägelser

Egenskapen `overridePredictions` Options anger att om användaren skickar en extern entitet som överlappar med en förväntad entitet med samma namn, väljer Luis den enhet som har skickats eller entiteten som finns i modellen. 

Anta till exempel frågan `today I'm free`. Luis identifierar `today` som en datetimeV2 med följande svar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om användaren skickar den externa entiteten:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Om är inställt `false`på, returnerar Luis ett svar som om den externa entiteten inte skickades. `overridePredictions` 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om är inställt `true`på, returnerar Luis ett svar, inklusive: `overridePredictions`

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Lösning

Den _valfria_ `resolution` egenskapen returneras i förutsägelse svaret, så att du kan skicka de metadata som är associerade med den externa entiteten och sedan ta emot den igen i svaret. 

Det primära syftet är att utöka fördefinierade entiteter, men den är inte begränsad till den typen av enhet. 

`resolution` Egenskapen kan vara ett tal, en sträng, ett objekt eller en matris:

* "Dallas"
* {"text": "värde"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamiska listor som har överförts vid förutsägelse tiden

Med dynamiska listor kan du utöka en befintlig utbildad och publicerad List-entitet, redan i LUIS-appen. 

Använd den här funktionen när du behöver ändra listan med enhets värden. Med den här funktionen kan du utöka en entitet som redan har tränats och publicerats:

* Vid tiden för slut punkten för förfrågan förutsägelse.
* För en enskild begäran.

List entiteten kan vara tom i LUIS-appen, men den måste finnas. List-entiteten i LUIS-appen har inte ändrats, men förutsägelse funktionen i slut punkten utökas till att innehålla upp till 2 listor med cirka 1 000 objekt.

### <a name="dynamic-list-json-request-body"></a>Brödtext för dynamisk lista JSON-begäran

Skicka i följande JSON-brödtext för att lägga till en ny under lista med synonymer till listan och Förutsäg List entiteten för texten, `LUIS` `POST` med frågan förutsägelse-begäran:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Förutsägelse-svaret innehåller List entitet, med alla andra förväntade entiteter, eftersom den definieras i begäran. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset har bytt namn till datetimeReference

**I v2** `timezoneOffset` skickas [parametern](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) i förutsägelse förfrågan som en parameter för frågesträng, oavsett om begäran skickas som en get-eller post-begäran. 

**I v3**tillhandahålls samma funktion med post text parametern `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Markera placering av entiteter i yttranden

**I v2**har en entitet marker ATS i en uttryck med `startIndex` och. `endIndex` 

**I v3**markeras entiteten med `startIndex` och `entityLength`.

## <a name="deprecation"></a>Utfasning 

V2-API: t är inte inaktuellt i minst 9 månader efter för hands versionen av v3. 

## <a name="next-steps"></a>Nästa steg

Använd v3 API-dokumentationen för att uppdatera befintliga REST-anrop till LUIS [slut punkts](https://aka.ms/luis-api-v3) -API: er. 
