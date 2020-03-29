---
title: Dataextrahering - LUIS
description: Extrahera data från uttryckstext med avsikter och entiteter. Läs om vilken typ av data som kan extraheras från Språk understanding (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221088"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrahera data från uttryckstext med avsikter och entiteter
LUIS ger dig möjlighet att hämta information från en användares yttranden på naturligt språk. Informationen extraheras på ett sätt som kan användas av ett program, ett program eller en chattrobot för att vidta åtgärder. I följande avsnitt kan du läsa vilka data som returneras från avsikter och entiteter med exempel på JSON.

De svåraste data att extrahera är maskininlärda data eftersom det inte är en exakt textmatchning. Datautvinning av de maskininlärda [entiteterna](luis-concept-entity-types.md) måste vara en del av [redigeringscykeln](luis-concept-app-iteration.md) tills du är säker på att du får de data du förväntar dig.

## <a name="data-location-and-key-usage"></a>Dataplats och nyckelanvändning
LUIS tillhandahåller data från den publicerade [slutpunkten](luis-glossary.md#endpoint). **HTTPS-begäran** (POST eller GET) innehåller uttryck samt vissa valfria konfigurationer som mellanlagring eller produktionsmiljöer.

#### <a name="v2-prediction-endpoint-request"></a>[V2-begäran om förutsägelseslutpunkt](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[V3-begäran om för förutsägelseslutpunkt](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

Den `appID` är tillgänglig på sidan **Inställningar** i LUIS-appen samt `/apps/`en del av webbadressen (efter) när du redigerar luis-appen. Slutpunktsnyckeln `subscription-key` som används för att fråga appen. Även om du kan använda din kostnadsfria redigerings-/startnyckel medan du lär dig LUIS, är det viktigt att ändra slutpunktsnyckeln till en nyckel som stöder din [förväntade LUIS-användning](luis-boundaries.md#key-limits). Enheten `timezoneOffset` är minuter.

**HTTPS-svaret** innehåller all avsikts- och entitetsinformation LUIS kan avgöra baserat på den aktuella publicerade modellen för antingen mellanlagrings- eller produktionsslutpunkten. Slutpunkts-URL:en finns på [LUIS-webbplatsen,](luis-reference-regions.md) i avsnittet **Hantera,** på sidan **Nycklar och slutpunkter.**

## <a name="data-from-intents"></a>Data från avsikter
Den primära informationen är det högsta **poängmetodnamnet**. Slutpunktssvaret är:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|
|Avsikt|String|topScoringIntent.intent|"GetStoreInfo"|

Om din chatbot- eller LUIS-samtalsapp fattar ett beslut baserat på mer än en avsiktspoäng returnerar du alla avsikters poäng.


#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

Ange frågestreparametern `verbose=true`. Slutpunktssvaret är:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Ange frågestreparametern `show-all-intents=true`. Slutpunktssvaret är:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

Avsikterna sorteras från högsta till lägsta poäng.

|Dataobjekt|Datatyp|Dataplats|Värde|Poäng|
|--|--|--|--|:--|
|Avsikt|String|avsikter[0].avsikt|"GetStoreInfo"|0.984749258|
|Avsikt|String|avsikter[1].avsikt|"Ingen"|0.0168218873|

Om du lägger till fördefinierade domäner anger avsiktsnamnet domänen, till exempel `Utilties` eller `Communication` samt avsikten:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

|Domain|Dataobjekt|Datatyp|Dataplats|Värde|
|--|--|--|--|--|
|Verktyg|Avsikt|String|avsikter[0].avsikt|"<b>Verktyg</b>. ShowNext"|
|Kommunikation|Avsikt|String|avsikter[1].avsikt|<b>Kommunikation</b>. StartOver"|
||Avsikt|String|avsikter[2].avsikt|"Ingen"|


## <a name="data-from-entities"></a>Data från entiteter
De flesta chatbots och program behöver mer än avsiktsnamnet. Dessa ytterligare, valfria data kommer från entiteter som identifierats i uttryck. Varje typ av entitet returnerar olika information om matchningen.

Ett enda ord eller en fras i ett uttryck kan matcha mer än en entitet. I så fall returneras varje matchande entitet med sin poäng.

Alla entiteter returneras i **entitetsmatrisen** för svaret från slutpunkten:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Tokeniserad entitet returnerad

Granska [tokensupporten](luis-language-support.md#tokenization) i LUIS.

## <a name="simple-entity-data"></a>Enkla entitetsdata

En [enkel entitet](reference-entity-simple.md) är ett maskininlärt värde. Det kan vara ett ord eller en fras.

## <a name="composite-entity-data"></a>Sammansatta entitetsdata

En [sammansatt entitet](reference-entity-composite.md) består av andra entiteter, till exempel fördefinierade entiteter, enkla, reguljära uttryck och listentiteter. De separata entiteterna utgör en hel entitet.

## <a name="list-entity-data"></a>Lista entitetsdata

[Listentiteter representerar](reference-entity-list.md) en fast, sluten uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listentiteter. Använd funktionen **Rekommendera** om du vill se förslag på nya ord baserat på den aktuella listan. Om det finns mer än en listentitet med samma värde returneras varje entitet i slutpunktsfrågan.

## <a name="prebuilt-entity-data"></a>Fördefinierade entitetsdata
[Fördefinierade entiteter](luis-concept-entity-types.md) identifieras baserat på en reguljär uttrycksmatchning med projektet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) med öppen källkod. Fördefinierade entiteter returneras i entitetsmatrisen och `builtin::`använder typnamnet som föregås av . Följande text är ett exempel yttrande med den returnerade fördefinierade entiteter:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Utan frågestringsparametern: `verbose=true`

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Med frågestringsparametern: `verbose=true`

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Entitetsdata för reguljära uttryck

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger.

## <a name="extracting-names"></a>Extrahera namn
Det är svårt att få namn från ett uttryck eftersom ett namn kan vara nästan vilken kombination som helst av bokstäver och ord. Beroende på vilken typ av namn du extraherar har du flera alternativ. Följande förslag är inte regler utan fler riktlinjer.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Lägg till fördefinierade personnamn och geographyV2-entiteter

[PersonName](luis-reference-prebuilt-person.md) och [GeographyV2-entiteter](luis-reference-prebuilt-geographyV2.md) finns i vissa [språkkulturer](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Namn på personer

Människors namn kan ha några små format beroende på språk och kultur. Använd antingen en fördefinierad **[personName-entitet](luis-reference-prebuilt-person.md)** eller en **[enkel entitet](luis-concept-entity-types.md#simple-entity)** med [roller](luis-concept-roles.md) för- och efternamn.

Om du använder den enkla entiteten, se till att ge exempel som använder för- och efternamn i olika delar av uttryck, i uttryck av olika längder och yttranden över alla avsikter, inklusive avsikten Ingen. [Granska](luis-how-to-review-endoint-utt.md) slutpunktsyttranden regelbundet för att märka alla namn som inte förutsågs korrekt.

### <a name="names-of-places"></a>Namn på platser

Platsnamn anges och kallas städer, län, delstater, provinser och länder/regioner. Använd den fördefinierade entiteten **[geographyV2](luis-reference-prebuilt-geographyv2.md)** för att extrahera platsinformation.

### <a name="new-and-emerging-names"></a>Nya och nya namn

Vissa appar måste kunna hitta nya och nya namn som produkter eller företag. Dessa typer av namn är den svåraste typen av dataextrahering. Börja med en **[enkel entitet](luis-concept-entity-types.md#simple-entity)** och lägg till en [fraslista](luis-concept-feature.md). [Granska](luis-how-to-review-endoint-utt.md) slutpunktsyttranden regelbundet för att märka alla namn som inte förutsågs korrekt.

## <a name="pattern-roles-data"></a>Mönsterroller data
Roller är kontextuella skillnader mellan entiteter.


#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

Entitetsnamnet är `Location` `Origin` , `Destination`med två roller och .

```JSON
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
```

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

I V3 är **rollnamnet** objektets primära namn.

Entitetsnamnet är `Location` `Origin` , `Destination`med två roller och .

Utan frågestringsparametern: `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Med frågestringsparametern: `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Pattern.any entitetsdata

[Pattern.any](reference-entity-pattern-any.md) är en platshållare med variabel längd som endast används i ett mönsters malluttryck för att markera var entiteten börjar och slutar.

## <a name="sentiment-analysis"></a>Sentimentanalys
Om Sentiment-analys har konfigurerats innehåller LUIS-json-svaret sentimentanalys. Läs mer om sentimentanalys i [dokumentationen till Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

### <a name="sentiment-data"></a>Sentimentdata
Sentimentdata är en poäng mellan 1 och 0 som anger den positiva (närmare 1) eller negativa (närmare 0) sentimentet för data.

När kulturen `en-us`är är svaret:

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


### <a name="key-phrase-extraction-entity-data"></a>Information om extrahering av nyckelfraser
Entiteten för nyckelfrasextrahering returnerar nyckelfraser i uttrycket som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Utan frågestringsparametern: `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Med frågestringsparametern: `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter

LUIS returnerar alla entiteter som har identifierats i uttrycket. Som ett resultat kan din chatbot behöva fatta beslut baserat på resultaten. Ett uttryck kan ha många entiteter i ett uttryck:

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS-slutpunkten kan identifiera samma data i olika entiteter.

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Utan `verbose=true` som frågestrålningsparameter.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Med `verbose=true` som frågeparameter.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Datamatchning av flera listentiteter

Om ett ord eller en fras matchar mer än en listentitet returnerar slutpunktsfrågan varje listentitet.

För frågan `when is the best time to go to red rock?`och appen innehåller `red` ordet i mer än en lista känner LUIS igen alla entiteter och returnerar en matris med entiteter som en del av JSON-slutpunktssvaret:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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



#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Utan `verbose=true` i frågesträngen:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Med `verbose=true` i frågesträngen:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Nästa steg

Se [Lägga till entiteter](luis-how-to-add-entities.md) om du vill veta mer om hur du lägger till entiteter i LUIS-appen.
