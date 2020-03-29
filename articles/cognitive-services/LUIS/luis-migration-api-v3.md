---
title: Förutsägelseslutpunktsändringar i V3-API:et
description: Slutpunkt V3-API:erna för frågeförutsägelser har ändrats. Använd den här guiden för att förstå hur du migrerar till version 3-slutpunkts-API:er.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117383"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Förutsägelseslutpunktsändringar för V3

Slutpunkt V3-API:erna för frågeförutsägelser har ändrats. Använd den här guiden för att förstå hur du migrerar till version 3-slutpunkts-API:er.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Allmänt tillgänglig status** - detta V3 API innehåller betydande JSON-begäran och svarsändringar från V2 API.

V3 API innehåller följande nya funktioner:

* [Externa enheter](#external-entities-passed-in-at-prediction-time)
* [Dynamiska listor](#dynamic-lists-passed-in-at-prediction-time)
* [Fördefinierade entitet JSON-ändringar](#prebuilt-entity-changes)

Begäran och [svaret](#response-changes) [för](#request-changes) förutsägelseslutpunkt har betydande ändringar för att stödja de nya funktionerna ovan, inklusive följande:

* [Ändringar av svarsobjekt](#top-level-json-changes)
* [Referenser till entitetsrollnamn i stället för entitetsnamn](#entity-role-name-instead-of-entity-name)
* [Egenskaper som ska markera entiteter i yttranden](#marking-placement-of-entities-in-utterances)

[Referensdokumentation](https://aka.ms/luis-api-v3) finns tillgänglig för V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3-ändringar från förhandsgranskning till GA

V3 gjorde följande ändringar som en del av flytten till GA:

* Följande fördefinierade entiteter har olika JSON-svar:
    * [Ordning och med V1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatumtidV2](luis-reference-prebuilt-datetimev2.md)
    * Mätbara enhetsnyckelnamn `units` från till`unit`

* Begär brödtext JSON ändring:
    * från `preferExternalEntities` till`preferExternalEntities`
    * valfri `score` parameter för externa entiteter

* Svarsorgan JSON förändringar:
    * `normalizedQuery`Bort

## <a name="suggested-adoption-strategy"></a>Föreslagen adoptionsstrategi

Om du använder Bot Framework, Bing Spell Check V7, eller vill migrera luis-appförfattaren, fortsätter du att använda V2-slutpunkten.

Om du inte vet att inget av klientprogrammet eller -integreringarna (Bot Framework och Bing Spell Check V7) påverkas och du är bekväm med att migrera luis-appförfattaren och förutsägelseslutpunkten samtidigt börjar du använda slutpunkten V3-förutsägelse. V2 förutsägelse slutpunkt kommer fortfarande att vara tillgänglig och är en bra back strategi.


## <a name="not-supported"></a>Stöds inte

### <a name="bing-spell-check"></a>Stavningskontroll i Bing

Det här API:et stöds inte i V3 prediction endpoint - fortsätt att använda V2 API-förutsägelseslutpunkten för stavningskorrigeringar. Om du behöver stavningskorrigering när du använder V3 API, har klientprogrammet anropa [API för stavningskontroll](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) av Bing och ändra texten till rätt stavning innan du skickar texten till LUIS API.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework- och Azure Bot Service-klientprogram

Fortsätt att använda V2 API-förutsägelseslutpunkten tills V4.7 i Bot Framework släpps.

## <a name="v2-api-deprecation"></a>V2 API-utfasning

V2 prediction API kommer inte att vara inaktuella i minst 9 månader efter V3-förhandsversionen, 8 juni 2020.

## <a name="endpoint-url-changes"></a>Url-ändringar för slutpunkt

### <a name="changes-by-slot-name-and-version-name"></a>Ändringar efter kortplatsnamn och versionsnamn

Formatet på HTTP-anropet för V3-slutpunkten har ändrats.

Om du vill fråga efter version måste du `"directVersionPublish":true`först publicera via [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) med . Fråga slutpunkten som refererar till versions-ID i stället för platsnamnet.

|VERSION AV FÖRUTSÄGELSE-API|Metod|URL|
|--|--|--|
|V3 (på andra)|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>förutsägelse</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3 (på andra)|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>förutsägelse</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|V2|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>förutsägelse</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>förutsägelse</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Giltiga värden för`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Begära ändringar

### <a name="query-string-changes"></a>Ändringar av frågesträng

V3-API:et har olika frågesträngparametrar.

|Param namn|Typ|Version|Default|Syfte|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Lagra fråga i loggfilen. Standardvärdet är falskt.|
|`query`|sträng|Endast V3|Ingen standard - det krävs i GET-begäran|**I V2**finns uttrycket som ska `q` förutsägas i parametern. <br><br>**I V3**skickas funktionen i `query` parametern.|
|`show-all-intents`|boolean|Endast V3|false|Returnera alla avsikter med motsvarande poäng i objektet **prediction.intents.** Avsikter returneras som objekt i `intents` ett överordnat objekt. Detta möjliggör programmatisk åtkomst utan att behöva hitta `prediction.intents.give`avsikten i en matris: . I V2 returnerades dessa i en matris. |
|`verbose`|boolean|V2 & V3|false|**I V2**, när den är inställd på true, returnerades alla förväntade avsikter. Om du behöver alla förväntade avsikter, använd V3-paramen `show-all-intents`.<br><br>**I V3**tillhandahåller den här parametern endast information om entitetsmetadata om entitetsförutsägels.  |
|`timezoneOffset`|sträng|V2|-|Tidszon tillämpas på datetimeV2 entiteter.|
|`datetimeReference`|sträng|V3 (på andra)|-|[Tidszon tillämpas](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) på datetimeV2 entiteter. Ersätter `timezoneOffset` från V2.|


### <a name="v3-post-body"></a>V3 POST-brödtext

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Egenskap|Typ|Version|Default|Syfte|
|--|--|--|--|--|
|`dynamicLists`|matris|Endast V3|Krävs inte.|[Med dynamiska listor](#dynamic-lists-passed-in-at-prediction-time) kan du utöka en befintlig tränad och publicerad listentitet, som redan finns i LUIS-appen.|
|`externalEntities`|matris|Endast V3|Krävs inte.|[Externa entiteter](#external-entities-passed-in-at-prediction-time) ger LUIS-appen möjlighet att identifiera och märka entiteter under körning, vilket kan användas som funktioner för befintliga entiteter. |
|`options.datetimeReference`|sträng|Endast V3|Ingen standard|Används för att bestämma [datetimeV2-förskjutning](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formatet för datetimeReference är [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Endast V3|false|Anger om användarens [externa entitet (med samma namn som befintlig enhet)](#override-existing-model-predictions) används eller om den befintliga entiteten i modellen används för förutsägelse. |
|`query`|sträng|Endast V3|Krävs.|**I V2**finns uttrycket som ska `q` förutsägas i parametern. <br><br>**I V3**skickas funktionen i `query` parametern.|



## <a name="response-changes"></a>Svarsändringar

Frågesvaret JSON ändrades för att ge större programmatisk åtkomst till de data som används oftast.

### <a name="top-level-json-changes"></a>JSON-ändringar på högsta nivå



De översta JSON-egenskaperna för `verbose` V2 är, när den är inställd på `intents` true, som returnerar alla avsikter och deras poäng i egenskapen:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De bästa JSON-egenskaperna för V3 är:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Objektet `intents` är en oordnad lista. Anta inte att det `intents` första barnet `topIntent`i motsvarar . Använd i `topIntent` stället värdet för att hitta poängen:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Ändringarna i JSON-schemat tillåter:

* Tydlig distinktion mellan `query`ursprungligt uttryck och `prediction`returnerad förutsägelse.
* Enklare programmatisk åtkomst till förväntade data. I stället för att räkna upp en matris i V2 kan du komma åt värden **efter namn** för både avsikter och entiteter. För förväntade entitetsroller returneras rollnamnet eftersom det är unikt i hela appen.
* Datatyper, om de fastställs, respekteras. Numeriska enheter returneras inte längre som strängar.
* Skillnad mellan första prioritet förutsägelse information och `$instance` ytterligare metadata, returneras i objektet.

### <a name="entity-response-changes"></a>Förändringar i entitetssvar

#### <a name="marking-placement-of-entities-in-utterances"></a>Markera placering av entiteter i yttranden

**I V2**markerades en entitet `startIndex` i `endIndex`ett uttryck med och .

**I V3**markeras entiteten med `startIndex` och `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Åtkomst `$instance` för entitetsmetadata

Om du behöver entitetsmetadata `verbose=true` måste frågesträngen använda flaggan `$instance` och svaret innehåller metadata i objektet. Exempel visas i JSON-svaren i följande avsnitt.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Varje förväntad entitet representeras som en matris

Objektet `prediction.entities.<entity-name>` innehåller en matris eftersom varje entitet kan förutsägas mer än en gång i uttryck.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Fördefinierade entitetsändringar

V3-svarsobjektet innehåller ändringar av fördefinierade entiteter. Granska [specifika fördefinierade entiteter](luis-reference-prebuilt-entities.md) för att lära dig mer.

#### <a name="list-entity-prediction-changes"></a>Lista ändringar av entitetsförutsägelse

JSON för en lista entitet förutsägelse har ändrats till en matris:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Varje inre matris motsvarar text inuti uttrycket. Det inre objektet är en matris eftersom samma text kan visas i mer än en underlista till en listentitet.

När du `entities` mappar mellan `$instance` objektet till objektet bevaras ordningen på objekten för listentitetens förutsägelser.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Entitetsrollnamn i stället för entitetsnamn

I V2 `entities` returnerade matrisen alla förväntade entiteter med entitetsnamnet som unik identifierare. I V3, om entiteten använder roller och förutsägelsen är för en entitetsroll, är den primära identifieraren rollnamnet. Detta är möjligt eftersom entitetsrollnamn måste vara unika i hela appen, inklusive andra modellnamn (avsikt, entitet) .

I följande exempel: betrakta ett uttryck som `Yellow Bird Lane`innehåller texten . Den här texten förutses `Location` som en `Destination`anpassad entitets roll som .

|Yttrandetext|Entitetsnamn|Rollnamn|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

I V2 identifieras entiteten av _entitetsnamnet_ med rollen som egenskap för objektet:

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

I V3 refereras entiteten av _entitetsrollen,_ om förutsägelsen är för rollen:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

I V3, samma resultat `verbose` med flaggan för att returnera entitet metadata:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Externa entiteter passerade in vid prediktionstillfälle

Externa entiteter ger LUIS-appen möjlighet att identifiera och märka entiteter under körning, vilket kan användas som funktioner för befintliga entiteter. På så sätt kan du använda dina egna separata och anpassade entitetsutdragorer innan du skickar frågor till din förutsägelseslutpunkt. Eftersom detta görs vid slutpunkten för frågeprediktion behöver du inte träna om och publicera modellen.

Klientprogrammet tillhandahåller sin egen entitetsutsugare genom att hantera entitetsmatchning och bestämma platsen inom uttryck för den matchade entiteten och sedan skicka den informationen med begäran.

Externa entiteter är mekanismen för att utöka alla entitetstyper samtidigt som de används som signaler till andra modeller som roller, sammansatta och andra.

Detta är användbart för en entitet som har data som endast är tillgängliga vid körning av frågeprognoser. Exempel på den här typen av data är att ständigt ändra data eller specifika per användare. Du kan utöka en LUIS-kontaktentitet med extern information från en användares kontaktlista.

### <a name="entity-already-exists-in-app"></a>Entiteten finns redan i appen

Värdet `entityName` för den externa entiteten, som skickades i post-brödtexten POST för slutpunktsbegäran, måste redan finnas i den tränade och publicerade appen när begäran görs. Typen av entitet spelar ingen roll, alla typer stöds.

### <a name="first-turn-in-conversation"></a>Första sväng i konversation

Överväg ett första uttryck i en chattrobotkonversation där en användare anger följande ofullständiga information:

`Send Hazem a new message`

Begäran från chattroboten till LUIS kan skicka `Hazem` in information i POST-brödtexten om så att den matchas direkt som en av användarens kontakter.

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

Förutsägelsesvaret inkluderar den externa entiteten, med alla andra förväntade entiteter, eftersom det definieras i begäran.

### <a name="second-turn-in-conversation"></a>Andra sväng i konversation

Nästa användare yttrande i chatten bot använder en mer term:

`Send him a calendar reminder for the party.`

I föregående uttryck används `him` uttrycket som en `Hazem`referens till . Den konversationschatt bot, i POST `him` kroppen, kan mappa till entitetsvärdet extraheras från den första yttrande, `Hazem`.

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

Förutsägelsesvaret inkluderar den externa entiteten, med alla andra förväntade entiteter, eftersom det definieras i begäran.

### <a name="override-existing-model-predictions"></a>Åsidosätt befintliga modellprognoser

Egenskapen `preferExternalEntities` options anger att om användaren skickar en extern entitet som överlappar en förväntad entitet med samma namn, väljer LUIS den entitet som skickas in eller den entitet som finns i modellen.

Tänk till exempel `today I'm free`på frågan . LUIS identifierar `today` som en datetimeV2 med följande svar:

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

Om `preferExternalEntities` är inställd `false`på returnerar LUIS ett svar som om den externa entiteten inte skickades.

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

Om `preferExternalEntities` är inställd `true`på returnerar LUIS ett svar inklusive:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Lösning

Den _valfria_ `resolution` egenskapen returnerar i förutsägelsesvaret, så att du kan skicka in metadata som är associerade med den externa entiteten och sedan ta emot den tillbaka i svaret.

Det primära syftet är att utöka fördefinierade entiteter, men det är inte begränsat till den entitetstypen.

Egenskapen `resolution` kan vara ett tal, en sträng, ett objekt eller en matris:

* "Dallas"
* {"text": "värde"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamiska listor som skickas in vid förutsägelsetillfället

Med dynamiska listor kan du utöka en befintlig tränad och publicerad listentitet, som redan finns i LUIS-appen.

Använd den här funktionen när listentitetens värden måste ändras med jämna mellanrum. Med den här funktionen kan du utöka en redan tränad och publicerad listentitet:

* Vid tidpunkten för slutpunktsbegäran för frågeförutsägels.
* För en enda begäran.

Listentiteten kan vara tom i LUIS-appen, men den måste finnas. Listentiteten i LUIS-appen ändras inte, men förutsägelsemöjligheten vid slutpunkten utökas till att omfatta upp till 2 listor med cirka 1 000 objekt.

### <a name="dynamic-list-json-request-body"></a>Dynamisk lista JSON-begäran

Skicka följande JSON-bröd för att lägga till en ny underlista med synonymer `LUIS`i listan `POST` och förutsäga listentiteten för texten, med begäran om frågeförutsägelse:

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

Förutsägelsesvaret innehåller den listentiteten, med alla andra förväntade entiteter, eftersom den definieras i begäran.

## <a name="deprecation"></a>Utfasning

V2 API kommer inte att vara inaktuella i minst 9 månader efter V3-förhandsgranskningen.

## <a name="next-steps"></a>Nästa steg

Använd V3 API-dokumentationen för att uppdatera befintliga REST-anrop till [LUIS-endpoint](https://aka.ms/luis-api-v3) API:er.
