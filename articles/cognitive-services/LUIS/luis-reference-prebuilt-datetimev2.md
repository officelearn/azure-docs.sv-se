---
title: DatetimeV2 fördefinierade entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln har datetimeV2 fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 8c29ebd675bb6af66203c13824dacbe9ea2421a2
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732803"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2-fördefinierad entitet för en LUIS-app

Den **datetimeV2** fördefinierade entitet extraherar värdena för datum och tid. Dessa värden lösa i ett standardformat för klientprogram kan använda. När ett uttryck har datum och tid som inte är klar, LUIS innehåller _både tidigare och framtida värden_ reaktion slutpunkt. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller datetimeV2 till programmet avsikter.

## <a name="types-of-datetimev2"></a>Typer av datetimeV2
DatetimeV2 hanteras från [identifierare-text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) lagringsplatsen.

## <a name="example-json"></a>Exempel på JSON

Följande uttryck och dess partiella JSON-svar visas nedan.

`8am on may 2nd 2019`

#### <a name="v3-responsetab1-1"></a>[V3-svar](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab1-2"></a>[V3 utförlig Response](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-responsetab1-3"></a>[V2-svar](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Egenskapsnamn |Egenskapstypen och beskrivning|
|---|---|
|Enhet|**sträng** -Text som extraherats från uttryck med typen av datum, tid, datum eller tidsintervall.|
|typ|**sträng** – en av de [undertyper till datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** -index i uttryck då entiteten börjar.|
|endIndex|**int** -index i uttryck då entiteten har upphört.|
|lösning|Har en `values` matris som har en, två eller fyra [värdena för lösning](#values-of-resolution).|
|end|Slutvärdet för en tid eller ett intervall, i samma format som `value`. Används endast om `type` är `daterange`, `timerange`, eller `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Undertyper till datetimeV2

Den **datetimeV2** fördefinierade entitet har följande undertyper och exempel på var och en finns i tabellen nedan:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Värdena för lösning
* Matrisen innehåller ett element om datum och tid i uttryck är helt angivna och entydiga.
* Matrisen innehåller två element om datetimeV2-värdet är tvetydig. Tvetydighet innehåller brist på specifika år, en tid eller tidsintervall. Se [tvetydiga datum](#ambiguous-dates) exempel. När tiden är tvetydig för am. eller körs båda värdena ingår.
* Matrisen har fyra element om uttryck innehåller två element med tvetydighet. Denna tvetydighet innehåller element som har:
  * Ett datum eller ett intervall som är tvetydig avseende år
  * En tid eller ett tidsintervall som är tvetydig avseende A.M. eller PM Till exempel 3:00 den 3 April.

Varje element i den `values` matris kan ha följande fält:

|Egenskapsnamn|Beskrivning av egenskap|
|--|--|
|Timex|tid, datum eller ett intervall som är uttryckt i TIMEX-format som följer den [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601) och TIMEX3 attribut för anteckningens med TimeML språk. Den här anteckningen beskrivs i den [TIMEX riktlinjer](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|rest|term som används för att beskriva hur du använder värdet, till exempel `before`, `after`.|
|typ|Under typen, som kan vara något av följande: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|värde|**Valfritt.** Ett DateTime-objekt i formatet ÅÅÅÅ-MM-DD (Date), HH: mm: SS (Time) ÅÅÅÅ-MM-dd HH: mm: SS (datetime). Om `type` är `duration`, värdet är antalet sekunder (varaktighet) <br/> Används endast om `type` är `datetime` eller `date`, `time`, eller ”varaktighet.|

## <a name="valid-date-values"></a>Ogiltigt datum

Den **datetimeV2** stöder datumen mellan följande intervall:

| Min | Max. |
|----------|-------------|
| den 1 januari 1900   | den 31 December 2099 |

## <a name="ambiguous-dates"></a>Tvetydiga datum

Om datumet kan vara i förflutna eller framtida, LUIS innehåller båda värdena. Ett exempel är ett uttryck som innehåller månad och datum utan året.

Till exempel, med följande uttryck:

`May 2nd`

* Om dagens datum är den 3 2017, innehåller LUIS både ”2017-05-02” och ”2018-05-02” som värden.
* När dagens datum är den 1 maj 2017, innehåller LUIS både ”2016-05-02” och ”2017-05-02” som värden.

I följande exempel visas av lösningen på entitet som är ”maj 2”. Denna lösning förutsätter att dagens datum är ett datum mellan den 2 2017 och den 1 2018.
Fält med `X` i den `timex` fält är delar av datumet som inte uttryckligen anges i uttryck.

## <a name="date-resolution-example"></a>Exempel på datum matchning


Följande uttryck och dess partiella JSON-svar visas nedan.

`May 2nd`

#### <a name="v3-responsetab2-1"></a>[V3-svar](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab2-2"></a>[V3 utförlig Response](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-responsetab2-3"></a>[V2-svar](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum intervallet upplösning exempel för numeriska datum

Den `datetimeV2` entiteten extraherar datum- och tidsintervall. Den `start` och `end` anger början och slutet av intervallet. LUIS innehåller **DateRange** -värden för både innevarande år och nästa år för uttryck-`May 2nd to May 5th`. I den `timex` fält, de `XXXX` värden anger tvetydighet på året. `P3D` Anger hur lång tid är tre dagar långa.

Följande uttryck och dess partiella JSON-svar visas nedan.

`May 2nd to May 5th`

#### <a name="v3-responsetab3-1"></a>[V3-svar](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-responsetab3-2"></a>[V3 utförlig Response](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-responsetab3-3"></a>[V2-svar](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum intervallet upplösning exempel för dag i veckan

I följande exempel visas hur LUIS använder **datetimeV2** för att lösa uttryck-`Tuesday to Thursday`. I det här exemplet är det aktuella datumet 19 juni. LUIS innehåller **daterange** värden för båda datumintervall som före och efter det aktuella datumet.

Följande uttryck och dess partiella JSON-svar visas nedan.

`Tuesday to Thursday`

#### <a name="v3-responsetab4-1"></a>[V3-svar](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab4-2"></a>[V3 utförlig Response](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-responsetab4-3"></a>[V2-svar](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Tvetydig tid
Värden matrisen har två tidselement om den tid, eller tidsintervallet är tvetydig. Om det finns en tvetydig tid, har värden båda kl. och. gånger.

## <a name="time-range-resolution-example"></a>Exempel på tid intervallet namnmatchning

DatetimeV2 JSON-svaret har ändrats i API v3. I följande exempel visas hur LUIS använder **datetimeV2** att lösa det uttryck som har ett tidsintervall.

Ändringar från API v2:
* Egenskapen `datetimeV2.timex.type` returneras inte längre eftersom den returneras på den överordnade nivån `datetimev2.type`.
* Egenskapen `datetimeV2.value` har bytt namn till `datetimeV2.timex`.

Följande uttryck och dess partiella JSON-svar visas nedan.

`from 6pm to 7pm`

#### <a name="v3-responsetab5-1"></a>[V3-svar](#tab/5-1)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab5-2"></a>[V3 utförlig Response](#tab/5-2)

Följande JSON är med parametern `verbose` som har angetts till `true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-responsetab5-3"></a>[V2-svar](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Exempel på tids upplösning

Följande uttryck och dess partiella JSON-svar visas nedan.

`8am`

#### <a name="v3-responsetab6-1"></a>[V3-svar](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab6-2"></a>[V3 utförlig Response](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-responsetab6-3"></a>[V2-svar](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Föråldrad fördefinierade datum/tid

Den `datetime` fördefinierade entiteten är inaktuell och ersatts av **datetimeV2**.

Ersätt `datetime` med `datetimeV2` i din LUIS-app, gör du följande:

1. Öppna den **entiteter** rutan i LUIS-webbgränssnittet.
2. Ta bort den **datetime** fördefinierade entitet.
3. Klicka på **Lägg till fördefinierade entitet**
4. Välj **datetimeV2** och klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om den [dimension](luis-reference-prebuilt-dimension.md), [e-post](luis-reference-prebuilt-email.md) entiteter, och [nummer](luis-reference-prebuilt-number.md).

