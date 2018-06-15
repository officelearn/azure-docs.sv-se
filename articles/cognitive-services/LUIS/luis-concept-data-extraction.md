---
title: Förstå begrepp för extrahering i THOMAS - Azure | Microsoft Docs
description: Lär dig mer om vilken typ av data kan extraheras från språk förstå (THOMAS)
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ad9eaa966aafc4a9fc526d44d57c68cdaee3a5e0
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356137"
---
# <a name="data-extraction"></a>Extrahering av data
THOMAS ger dig möjlighet att hämta information från en användares naturligt språk utterances. Informationen hämtas på ett sätt att den kan användas av ett program, programmet eller chatbot för att vidta åtgärder.

Läs om vilka data som returneras från avsikter och entiteter med exempel på JSON i avsnitten nedan. Den svåraste data att extrahera är datorn lärt dig data eftersom den inte är en exakt matchning. Data extrahering av dator-inlärda [entiteter](luis-concept-entity-types.md) måste vara en del av den [redigering cykel](luis-concept-app-iteration.md) tills du är säker på att du tar emot data som du förväntar dig. 

## <a name="data-location-and-key-usage"></a>Dataanvändning för platsen och nyckel
THOMAS innehåller data från de publicerade [endpoint](luis-glossary.md#endpoint). Den **HTTPS-begäran** (POST eller GET) innehåller utterance samt vissa valfria konfigurationer, till exempel mellanlagring eller produktionsmiljö. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

Den `appID` är tillgängligt på den **inställningar** sidan i din THOMAS appen som en del av URL: en (när `/apps/`) när du redigerar THOMAS appen. Den `subscription-key` endpoint-nyckeln som används för att fråga efter din app. Du kan använda din kostnadsfria redigering/starter nyckel medan du lär dig THOMAS, är det viktigt att ändra prenumerationen nyckeln till en nyckel som har stöd för din [förväntades THOMAS användning](luis-boundaries.md#key-limits). Den `timezoneOffset` enhet är minuter.

Den **HTTPS svar** innehåller alla informationen avsikt och entiteten THOMAS kan fastställa baserat på den aktuella publicerade modellen antingen slutpunkten mellanlagring och produktion. Slutpunkten URL-Adressen finns på den [THOMAS] [ LUIS] webbplats **publicera** sidan. 

## <a name="data-from-intents"></a>Data från avsikter
Primära data är högsta resultatfunktioner **avsiktshantering namn**. Med hjälp av den `MyStore` [quickstart](luis-quickstart-intents-only.md), endpoint-svaret är:

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
|Avsikten|Sträng|topScoringIntent.intent|”GetStoreInfo”|

Om din chatbot eller THOMAS anropar app gör beslut baserat på mer än en avsiktshantering poäng, returnera avsikter resultat genom att ange parametern querystring `verbose=true`. Svaret från slutpunkten är:

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

Innehållet är ordnade från högsta till lägsta poäng.

|Dataobjekt|Datatyp|Dataplats|Värde|Poäng|
|--|--|--|--|:--|
|Avsikten|Sträng|avsikter [0] .intent|”GetStoreInfo”|0.984749258|
|Avsikten|Sträng|.intent avsikter [1]|”None”|0.0168218873|

Om du lägger till fördefinierade domäner avsiktshantering namnet anger domänen, t.ex `Utilties` eller `Communication` samt avsikten:

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
    
|Domän|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|--|
|Samhällsservice|Avsikten|Sträng|avsikter [0] .intent|”<b>Verktyg</b>. ShowNext ”|
|Kommunikation|Avsikten|Sträng|.intent avsikter [1]|<b>Kommunikation</b>. StartOver ”|
||Avsikten|Sträng|.intent avsikter [2]|”None”|


## <a name="data-from-entities"></a>Data från enheter
De flesta chatbots och program behöver mer än avsiktshantering namn. Dessa ytterligare och valfria data hämtas från enheter som identifieras i utterance. Varje typ av entitet returnerar olika typer av information om matchningen. 

Ett ord eller en fras i en utterance kan matcha flera enheter. I så fall returneras varje matchande entitet med dess resultat. 

Alla entiteter returneras i den **entiteter** matris av svaret från slutpunkten:

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

## <a name="tokenized-entity-returned"></a>Principfilerna entitet returnerades
Flera [kulturer](luis-supported-languages.md#tokenization) tillbaka enhetsobjekt med den `entity` värdet [tokeniserad](luis-glossary.md#token). StartIndex och endIndex som returneras av THOMAS i entitetsobjektet mappas inte till det nya, principfilerna värdet utan i stället på den ursprungliga frågan att extrahera rådata entiteten programmässigt. 

Till exempel i tyska, ordet `das Bauernbrot` tokeniserad till `das bauern brot`. Värdet principfilerna `das bauern brot`, returneras och det ursprungliga värdet programmässigt kan fastställas från startIndex och endIndex i den ursprungliga frågan, vilket ger dig `das Bauernbrot`.

## <a name="simple-entity-data"></a>Enkel enhetsdata

En [enkel enhet](luis-concept-entity-types.md) är en dator lärt dig värde. Det kan vara ett ord eller uttryck. 

`Bob Jones wants 3 meatball pho`

I tidigare utterance `Bob Jones` är märkt som en enkel `Customer` entitet.

De data som returneras från slutpunkten innehåller entitetsnamnet, identifierade texten från utterance, platsen för identifierade texten och poängsättningen:

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
|Enkel enhet|”Kunden”|”bob Karlsson”|

## <a name="hierarchical-entity-data"></a>Hierarkisk entitetsdata

[Hierarkiska](luis-concept-entity-types.md) entiteter är datorn lärt dig och kan innehålla ett ord eller uttryck. Underordnade identifieras av sammanhanget. Om du letar efter en överordnad-underordnad relation med exakt matchning, använda en [lista](#list-entity-data) entitet. 

`book 2 tickets to paris`

I tidigare utterance `paris` är märkt med en `Location::ToLocation` underordnad den `Location` hierarkiska entitet. 

De data som returneras från slutpunkten innehåller enhetsnamnet och underordnade namn, identifierade texten från utterance, platsen för identifierade texten och poängsättningen: 

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
|--|--|--|--|--|
|Hierarkisk entitet|Plats|ToLocation|”paris”|

## <a name="composite-entity-data"></a>Sammansatta entitetsdata
[Sammansatta](luis-concept-entity-types.md) entiteter är datorn lärt dig och kan innehålla ett ord eller uttryck. Anta till exempel att en sammansatt entitet av fördefinierade `number` och `Location::ToLocation` med följande utterance:

`book 2 tickets to paris`

Observera att `2`, antalet, och `paris`, ToLocation har ord mellan dem som inte är en del av någon av enheterna. Grön understrykning används i en märkt utterance i den [THOMAS] [ LUIS] webbplats, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

Sammansatta entiteter returneras i en `compositeEntities` matris och alla enheter i sammansatt returneras också i det `entities` matris:

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

## <a name="list-entity-data"></a>Entiteten listdata

En [lista](luis-concept-entity-types.md) entiteten är inte dator-lärt dig. Det är en exakt matchning. En lista representerar objekt i listan tillsammans med synonymer för dessa objekt. THOMAS markerar alla matchar till ett objekt i en lista som en entitet i svaret. En synonym kan vara mer än en lista. 

Anta att appen har en lista med namnet `Cities`, så att variationer av ort namn inklusive ort flygplats (Sea tac), flygplats (SEA), postnummer (98101), och phone riktnummer (206). 

|Listobjekt|Objektet synonymer|
|---|---|
|Seattle|SEA tac, sea, 98101, 206, + 1 |
|Paris|cdg roissy, ormation om sig själva, 75001, 1, +33|

`book 2 tickets to paris`

I den föregående utterance ordet `paris` mappas till paris-objektet som en del av den `Cities` listan entitet. Entiteten listan matchar både objektets normaliserat namn samt synonymer för objektet. 

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

Ett annat exempel utterance med hjälp av en synonym för Paris:

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
[Fördefinierade](luis-concept-entity-types.md) enheter identifieras baserat på ett reguljärt uttryck för matchning med öppen källkod [identifierare Text](https://github.com/Microsoft/Recognizers-Text) projekt. Fördefinierade entiteter returneras i matrisen entiteter och använda Ange namnet med prefixet `builtin::`. Följande är ett exempel utterance med returnerade fördefinierade enheter:

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
[Reguljärt uttryck](luis-concept-entity-types.md) enheter identifieras baserat på en vanlig uttrycksmatchning med ett uttryck som du anger när du skapar entiteten. När du använder `kb[0-9]{6}` som reguljärt uttryck Entitetsdefinition, följande JSON-svar är ett exempel utterance med entiteterna returnerade reguljärt uttryck för frågan `When was kb123456 published?`:

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
Det är svårt att få namn från en utterance eftersom ett namn kan vara nästan valfri kombination av bokstäver och ord. Beroende på vilken typ av namn som du extraherar, har du flera alternativ. Detta är inte regler men mer riktlinjer. 

### <a name="names-of-people"></a>Namnen på personer
Användares namn kan ha vissa mindre format beroende på språk och kultur. Använd en hierarkisk entitet med och efternamn som underordnade objekt eller en enkel enhet med roller i för- och efternamn. Se till att ge exempel som använder det första och sista namnet i olika delar av utterance i utterances av olika längd och utterances över alla avsikter inklusive ingen avsiktshantering. [Granska](label-suggested-utterances.md) endpoint utterances regelbundet att märka de namn som inte förutsade har korrekt. 

### <a name="names-of-places"></a>Namnen på platser
Platsnamn ställs och kända, till exempel städer, regioner, tillstånd, regioner och länder. Om din app använder en uppsättning platser informerad, bör en enhet i listan. Om du behöver att hitta alla placera namn kan skapa en enkel enhet och tillhandahålla olika exempel. Lägga till en fras lista över namn att förstärka vilken plats som ser ut som i din app. [Granska](label-suggested-utterances.md) endpoint utterances regelbundet att märka de namn som inte förutsade har korrekt. 

### <a name="new-and-emerging-names"></a>Nya och kommande namn
Vissa appar behöver för att kunna hitta nya och kommande namn, t.ex produkter eller företag. Det här är den svåraste typ av extrahering av data. Börja med en enkel enhet och lägga till en fras lista. [Granska](label-suggested-utterances.md) endpoint utterances regelbundet att märka de namn som inte förutsade har korrekt. 

## <a name="pattern-roles-data"></a>Mönstret roller data
Roller är sammanhangsberoende skillnaderna mellan enheter. 

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
Pattern.any enheter är variabel längd enheter används i mallen utterances av en [mönster](luis-concept-patterns.md). 

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


## <a name="sentiment-analysis"></a>Känsloanalys
Om Sentiment analys är konfigurerad, innehåller THOMAS json svaret sentiment analys. Mer information om sentiment analys i den [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentation.

### <a name="sentiment-data"></a>Sentiment data
Sentiment data är ett värde mellan 1 och 0 som anger positivt (närmare 1) eller ett negativt (närmare 0) sentiment av data.

När kulturen är `en-us`, svaret är:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

För alla andra kulturer är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Viktiga frasen extrahering entitetsdata
Viktiga frasen extrahering entiteten returnerar viktiga fraser i utterance, som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

<!-- TBD: verify JSON-->
```JSON
"keyPhrases": [
    "places",
    "beautiful views",
    "favorite trail"
]
```

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter
THOMAS returnerar alla enheter som identifieras i utterance. Din chatbot kan därför behöver fatta beslut baserat på resultatet. En utterance kan ha många entiteter i en utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

THOMAS slutpunkten kan identifiera samma data i olika enheter: 

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

## <a name="next-steps"></a>Nästa steg

Se [lägga till enheter](luis-how-to-add-entities.md) lära dig mer om hur du lägger till entiteter i appen THOMAS.

[LUIS]:luis-reference-regions.md