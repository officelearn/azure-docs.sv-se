---
title: Extrahering av data
titleSuffix: Language Understanding - Azure Cognitive Services
description: Extrahera data från uttryck text med avsikter och entiteter. Läs om vilken typ av data kan extraheras från Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 35f1521884de3a4a0971b6e1c00f92a9094a8550
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812807"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahera data från uttryck text med avsikter och entiteter
LUIS ger dig möjlighet att få information från en användares naturligt språk yttranden. Informationen hämtas i ett sätt att den kan användas av ett program, programmet eller chattrobot vidta åtgärder. Läs om vilka data returneras från avsikter och entiteter med exempel på JSON i avsnitten nedan.

Den svåraste data att extrahera är datorn lärt dig data eftersom det inte är en exakt denna matchning. Extrahering av data för den datorn-lärt dig [entiteter](luis-concept-entity-types.md) måste vara en del av den [redigering cykel](luis-concept-app-iteration.md) tills du är säker på att du får de data du förväntar dig.

## <a name="data-location-and-key-usage"></a>Dataanvändning för platsen och nyckel
LUIS innehåller data från den publicerade [endpoint](luis-glossary.md#endpoint). Den **HTTPS-begäran** (POST eller GET) innehåller uttryck samt vissa valfria konfigurationer, till exempel mellanlagring eller produktionsmiljö.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

Den `appID` är tillgänglig på den **inställningar** sidan av dina LUIS-app, samt en del av URL: en (när `/apps/`) när du redigerar den LUIS-app. Den `subscription-key` är slutpunktsnyckeln som används för att fråga din app. Du kan använda din kostnadsfria redigering/starter nyckel medan du lär LUIS, är det viktigt att ändra slutpunktsnyckeln till en nyckel som har stöd för din [förväntad användning av LUIS](luis-boundaries.md#key-limits). Den `timezoneOffset` enhet är minuter.

Den **HTTPS-svar** innehåller alla avsikt och entiteten informationen LUIS kan fastställa baserat på den aktuella publicerade modellen antingen slutpunkten mellanlagring eller produktion. Den slutpunkt som URL-Adressen finns på den [LUIS](luis-reference-regions.md) webbplats, i den **hantera** avsnittet på den **nycklar och slutpunkter** sidan.

## <a name="data-from-intents"></a>Data från avsikter
Primära data är högsta bedömning **avsikt namn**. Med hjälp av den `MyStore` [snabbstarten](luis-quickstart-intents-only.md), endpoint-svaret är:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|
|Avsikt|Sträng|topScoringIntent.intent|”GetStoreInfo”|

Om din chattrobot eller LUIS-anropa app fattar ett beslut som baseras på mer än en avsiktlig poäng, returnerar avsikter poäng genom att ange parametern querystring `verbose=true`. Slutpunkt-svaret är:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Avsikter sorteras från högsta till lägsta poängen.

|Dataobjekt|Datatyp|Dataplats|Värde|Poäng|
|--|--|--|--|:--|
|Avsikt|Sträng|avsikter [0] .intent|”GetStoreInfo”|0.984749258|
|Avsikt|Sträng|.intent avsikter [1]|”None”|0.0168218873|

Om du lägger till fördefinierade domäner avsikt namnet anger domänen, till exempel `Utilties` eller `Communication` samt avsikten:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domain|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|--|
|Samhällsservice|Avsikt|Sträng|avsikter [0] .intent|”<b>Verktyg</b>. ShowNext ”|
|Kommunikation|Avsikt|Sträng|.intent avsikter [1]|<b>Kommunikation</b>. StartOver ”|
||Avsikt|Sträng|.intent avsikter [2]|”None”|


## <a name="data-from-entities"></a>Data från enheter
De flesta chattrobotar och program behöver mer än avsikt namnet. Den här ytterligare, valfria data kommer från enheter som identifieras i uttryck. Varje typ av entitet returnerar olika typer av information om matchningen.

Ett enstaka ord eller en fras i ett uttryck kan matcha mer än en entitet. I så fall returneras varje matchande entitet med dess poäng.

Alla entiteter returneras i de **entiteter** matris för svaret från slutpunkten:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>principfilerna entitet returnerades
Flera [kulturer](luis-language-support.md#tokenization) returnera entitetsobjekt med den `entity` värdet [tokeniserad](luis-glossary.md#token). StartIndex och endIndex som returnerades av LUIS i entitetsobjektet mappas inte till det nya, principfilerna värdet utan istället på den ursprungliga frågan i ordning att extrahera raw entiteten programmässigt. 

Till exempel på tyska, ordet `das Bauernbrot` tokeniserad till `das bauern brot`. Värdet principfilerna `das bauern brot`, returneras och det ursprungliga värdet programmässigt kan fastställas från startIndex och endIndex av den ursprungliga frågan, vilket ger dig `das Bauernbrot`.

## <a name="simple-entity-data"></a>Enkel entitetsdata

En [enkel enhet](luis-concept-entity-types.md) är ett värde för datorn lärt dig. Det kan vara ett ord eller fraser.

`Bob Jones wants 3 meatball pho`

I den föregående uttryck `Bob Jones` är märkt som en enkel `Customer` entitet.

De data som returneras från slutpunkten innehåller entitetsnamnet, identifierade texten från uttryck, platsen för den identifierade texten och poängen:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel enhet|`Customer`|`bob jones`|

## <a name="hierarchical-entity-data"></a>Hierarkisk entitetsdata

**Hierarkisk entiteter gälla så småningom upphör att. Använd [entitet roller](luis-concept-roles.md) fastställa entitet underordnade typer, i stället för hierarkisk entiteter.**

[Hierarkisk](luis-concept-entity-types.md) entiteter är datorn lärt dig och kan innehålla ett ord eller fraser. Barn identifieras av kontext. Om du letar efter en överordnad-underordnad-relation med exakt denna matchning, använda en [lista](#list-entity-data) entitet.

`book 2 tickets to paris`

I den föregående uttryck `paris` är märkt med en `Location::ToLocation` underordnad den `Location` hierarkiska entitet.

De data som returneras från slutpunkten innehåller enhetens namn och namnet på underordnade, identifierade texten från uttryck, platsen för den identifierade texten och poängen:

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Dataobjekt|Överordnad|Underordnade|Värde|
|--|--|--|--|
|Hierarkisk entitet|Plats|ToLocation|”paris”|

## <a name="composite-entity-data"></a>Sammansatta entitetsdata
[Sammansatta](luis-concept-entity-types.md) entiteter är datorn lärt dig och kan innehålla ett ord eller fraser. Anta exempelvis att en sammansatt entitet av färdiga `number` och `Location::ToLocation` med följande uttryck:

`book 2 tickets to paris`

Observera att `2`, antalet, och `paris`, ToLocation har ord mellan dem som inte tillhör någon av enheterna. Grön linje, används i en taggade uttryck i den [LUIS](luis-reference-regions.md) webbplats, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

Sammansatta entiteter returneras i en `compositeEntities` matris och alla enheter i sammansatt returneras också i de `entities` matris:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Fördefinierade enhet - nummer|”builtin.number”|”2”|
|Hierarkisk enhet - plats|”Location::ToLocation”|”paris”|

## <a name="list-entity-data"></a>Lista entitetsdata

En [lista](luis-concept-entity-types.md) entiteten är inte datorn lärt dig. Det är en exakt denna matchning. En lista representerar objekt i listan tillsammans med synonymer för dessa objekt. LUIS markerar alla motsvarar ett objekt i en lista som en entitet i svaret. En synonym kan finnas i mer än en lista.

Anta att appen har en lista som heter `Cities`, så att variationer av stadsnamn inklusive stad flygplats (Sea tac), flygplatsen (SEA), postnummer (98101), och phone riktnummer (206).

|Listobjekt|Objektet synonymer|
|---|---|
|Seattle|SEA tac, hav, 98101, 206, + 1 |
|Paris|cdg, roissy, ormation om sig själva, 75001, 1, +33|

`book 2 tickets to paris`

I den föregående uttryck ordet `paris` mappas till paris-objektet som en del av den `Cities` lista entitet. Entiteten listan matchar både objektets normaliserade namn samt synonymer för objektet.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Ett annat exempel uttryck med en synonym för Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Fördefinierade entitetsdata
[Fördefinierade](luis-concept-entity-types.md) entiteter identifieras baserat på en vanlig uttrycksmatchning med öppen källkod [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text) projekt. Fördefinierade entiteter returneras i matrisen entiteter och använda namnet på prefixet `builtin::`. Följande text är ett exempel uttryck med returnerade förskapade entiteter:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Reguljärt uttryck entitetsdata
[Reguljärt uttryck](luis-concept-entity-types.md) entiteter identifieras baserat på en vanlig uttrycksmatchning med hjälp av ett uttryck som du anger när du har skapat entiteten. När du använder `kb[0-9]{6}` som reguljärt uttryck dess definition följande JSON-svar är en exempel-uttryck med entiteterna returnerade reguljärt uttryck för frågan `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Extrahera namn
Det är svårt att hämta namn från ett uttryck eftersom ett namn kan vara nästan vilken kombination av bokstäver och ord. Beroende på vilken typ av namn som du extrahera, har du flera alternativ. Följande rekommendationer är inte regler utan fler riktlinjer.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Lägg till fördefinierade PersonName och GeographyV2 entiteter

[PersonName](luis-reference-prebuilt-person.md) och [GeographyV2](luis-reference-prebuilt-geographyV2.md) entiteter är tillgängliga i vissa [språk kulturer](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Namnen på personer

Folkrepubliken namnet får inte innehålla något mindre format beroende på språket och kultur. Använd antingen fördefinierade **[personName](luis-reference-prebuilt-person.md)** entitet eller en **[enkel enhet](luis-concept-entity-types.md#simple-entity)** med [roller](luis-concept-roles.md) av första och efternamn. 

Om du använder enkel enhet, se till att ge exempel som använder det första och sista namnet i olika delar av uttryck i yttranden med olika längd och yttranden över alla avsikter inklusive ingen avsikt. [Granska](luis-how-to-review-endoint-utt.md) endpoint yttranden regelbundet att märka de namn som inte har förväntad korrekt.

### <a name="names-of-places"></a>Namnen på platser

Platsnamn anges och kända, till exempel städer, regioner, stater, regioner och länder. Använda fördefinierade entiteten **[geographyV2](luis-reference-prebuilt-geographyv2.md)** att extrahera platsinformation.

### <a name="new-and-emerging-names"></a>Nya och framväxande namn

Vissa appar behöver för att kunna hitta nya och framväxande namn, t.ex produkter eller företag. Dessa typer av namn är den svåraste typen av extrahering av data. Börja med en **[enkel enhet](luis-concept-entity-types.md#simple-entity)** och Lägg till en [frasen lista](luis-concept-feature.md). [Granska](luis-how-to-review-endoint-utt.md) endpoint yttranden regelbundet att märka de namn som inte har förväntad korrekt.

## <a name="pattern-roles-data"></a>Mönstret roller data
Roller är sammanhangsberoende skillnader med entiteter.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Pattern.any entitetsdata
Pattern.any entiteter är variabel längd entiteter som används i mallen yttranden av en [mönstret](luis-concept-patterns.md).

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Sentimentanalys
Om attitydanalys är konfigurerad, innehåller json-svar LUIS attitydanalys. Mer information om sentimentanalys i den [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentation.

### <a name="sentiment-data"></a>Åsiktsdata
Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data.

När kultur är `en-us`, svaret är:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

För alla andra kulturer och är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Entitetsdata med extrahering av diskussionsämne
Entiteten extrahering av diskussionsämne returnerar nyckelfraser i uttryck, som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter

LUIS returnerar alla entiteter som identifieras i uttryck. Din chattrobot kan därför behöva fatta beslut baserat på resultatet. Ett uttryck kan ha många entiteter i ett uttryck:

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS-slutpunkten kan identifiera samma data på olika enheter:

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Data som matchar flera listan entiteter

Om ett ord eller fraser matchar mer än en entitet i listan, returnerar endpoint-frågan varje entitet i listan.

För frågan `when is the best time to go to red rock?`, och appen har ordet `red` i mer än en LUIS identifierar alla entiteter och returnerar en matris med entiteter som en del av svaret för JSON-slutpunkten: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.
