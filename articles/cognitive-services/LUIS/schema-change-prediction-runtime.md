---
title: Utöka appen vid körning - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538582"
---
# <a name="extend-app-at-prediction-runtime"></a>Utöka appen vid förutsägelsekörning

Appens schema (modeller och funktioner) tränas och publiceras till slutpunkten för förutsägelse. Den här publicerade modellen används på förutsägelsekörningen. Du kan skicka ny information, tillsammans med användarens yttrande, till förutsägelsekörningen för att öka förutsägelsen.

Två ändringar i schemaändringarna för förutsägelsekörningar omfattar:
* [Externa enheter](#external-entities)
* [Dynamiska listor](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externa enheter

Externa entiteter ger LUIS-appen möjlighet att identifiera och märka entiteter under körning, vilket kan användas som funktioner för befintliga entiteter. På så sätt kan du använda dina egna separata och anpassade entitetsutdragorer innan du skickar frågor till din förutsägelseslutpunkt. Eftersom detta görs vid slutpunkten för frågeprediktion behöver du inte träna om och publicera modellen.

Klientprogrammet tillhandahåller sin egen entitetsutsugare genom att hantera entitetsmatchning och bestämma platsen inom uttryck för den matchade entiteten och sedan skicka den informationen med begäran.

Externa entiteter är mekanismen för att utöka alla entitetstyper samtidigt som de används som signaler till andra modeller.

Detta är användbart för en entitet som har data som endast är tillgängliga vid körning av frågeprognoser. Exempel på den här typen av data är att ständigt ändra data eller specifika per användare. Du kan utöka en LUIS-kontaktentitet med extern information från en användares kontaktlista.

Externa entiteter är en del av V3-redigerings-API:et. Läs mer om att [migrera](luis-migration-api-v3.md) till den här versionen.

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

I den här vändningen av `him` konversationen används `Hazem`uttrycket som en referens till . Den konversationschatt bot, i POST `him` kroppen, kan mappa till entitetsvärdet extraheras från den första yttrande, `Hazem`.

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

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamiska listor

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

## <a name="next-steps"></a>Nästa steg

* [Förutsägelseresultat](luis-concept-prediction-score.md)
* [Skapa ÄNDRINGAR AV API V3](luis-migration-api-v3.md)
