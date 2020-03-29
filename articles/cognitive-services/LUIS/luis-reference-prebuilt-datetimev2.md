---
title: DatetimeV2 Fördefinierade enheter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln har datetimeV2 fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270740"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 fördefinierad entitet för en LUIS-app

**DatetimeV2** fördefinierade entiteten extraherar datum- och tidsvärden. Dessa värden matchas i ett standardiserat format för klientprogram att använda. När ett uttryck har ett datum eller en tid som inte har slutförts, innehåller LUIS _både tidigare och framtida värden_ i slutpunktssvaret. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller datetimeV2 i programavsikterna.

## <a name="types-of-datetimev2"></a>Typer av datetimeV2
DatetimeV2 hanteras från [GitHub-databasen med identifierare-text.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>Exempel på JSON

Följande yttrande och dess partiella JSON-svar visas nedan.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3-svar](#tab/1-1)

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

#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/1-2)

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

#### <a name="v2-response"></a>[V2-svar](#tab/1-3)

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

|Egenskapsnamn |Egenskapstyp och beskrivning|
|---|---|
|Entitet|**sträng** - Text som extraherats från uttrycket med typ av datum, tid, datumintervall eller tidsintervall.|
|typ|**sträng** - En av [undertyperna av datetimeV2](#subtypes-of-datetimev2)
|startIndex (startIndex)|**int** - Indexet i uttrycket där entiteten börjar.|
|endIndex (på ett år)|**int** - Indexet i uttrycket som entiteten slutar på.|
|Upplösning|Har `values` en matris som har ett, två eller fyra [värden för upplösning](#values-of-resolution).|
|slut|Slutvärdet för en tid, eller datumintervall, `value`i samma format som . Används endast `type` `daterange`om `timerange`är , eller`datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Undertyper av datetimeV2

Den fördefinierade entiteten **datetimeV2** har följande undertyper och exempel på var och en finns i tabellen nedan:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Värden för upplösning
* Matrisen har ett element om datum eller tid i uttrycket är fullständigt angivet och entydigt.
* Matrisen har två element om datetimeV2-värdet är tvetydigt. Tvetydighet inkluderar brist på specifikt år, tid eller tidsintervall. Se [Tvetydiga datum](#ambiguous-dates) för exempel. När tiden är tvetydig för A.M. eller p.m., inkluderas båda värdena.
* Matrisen har fyra element om uttrycket har två element med tvetydighet. Denna tvetydighet innehåller element som har:
  * Ett datum- eller datumintervall som är tvetydigt i år
  * En tid eller ett tidsintervall som är tvetydigt när det gäller A.M. eller P.M. Till exempel 3:00 3 april.

Varje element `values` i matrisen kan ha följande fält:

|Egenskapsnamn|Beskrivning av egenskapen|
|--|--|
|Timex|tid, datum eller datumintervall uttryckt i TIMEX-format som följer [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601) och TIMEX3-attributen för anteckningar med hjälp av TimeML-språket. Den här anteckningen beskrivs i [TIMEX-riktlinjerna](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|Mod|term som används för att beskriva `before` `after`hur du använder värdet, till exempel , .|
|typ|Undertypen, som kan vara något `datetime`av `date` `time`följande `daterange` `timerange`objekt: , , , , , `datetimerange`, , `duration`, `set`.|
|värde|**Valfri.** Ett datetime-objekt i Format yyyy-MM-dd (datum), HH:mm:ss (tid) yyyy-MM-dd HH:mm:ss (datetime). Om `type` `duration`är , är värdet antalet sekunder (varaktighet) <br/> Används endast `type` `datetime` om `date` `time`är eller , eller varaktighet.|

## <a name="valid-date-values"></a>Giltiga datumvärden

**DatetimeV2** stöder datum mellan följande intervall:

| Min | Max |
|----------|-------------|
| Den 1 januari 1900   | den 31 december 2099 |

## <a name="ambiguous-dates"></a>Tvetydiga datum

Om datumet kan vara i det förflutna eller framtiden, ger LUIS båda värdena. Ett exempel är ett uttryck som innehåller månad och datum utan år.

Med tanke på följande uttryck:

`May 2nd`

* Om dagens datum är 3 maj 2017 tillhandahåller LUIS både "2017-05-02" och "2018-05-02" som värden.
* När dagens datum är 1 maj 2017 tillhandahåller LUIS både "2016-05-02" och "2017-05-02" som värden.

Följande exempel visar upplösningen för entiteten "2 maj". Denna resolution förutsätter att dagens datum är ett datum mellan 2 maj 2017 och 1 maj 2018.
Fält `X` med `timex` i fältet är delar av det datum som inte uttryckligen anges i uttryck.

## <a name="date-resolution-example"></a>Exempel på datummatchning


Följande yttrande och dess partiella JSON-svar visas nedan.

`May 2nd`

#### <a name="v3-response"></a>[V3-svar](#tab/2-1)

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

#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/2-2)

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

#### <a name="v2-response"></a>[V2-svar](#tab/2-3)

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Exempel på datumintervallmatchning för numeriskt datum

Entiteten `datetimeV2` extraherar datum- och tidsintervall. `start` Fälten `end` och anger början och slutet av intervallet. För uttryck `May 2nd to May 5th`ger LUIS **daterange-värden** för både innevarande år och nästa år. I `timex` fältet anger `XXXX` värdena årets tvetydighet. `P3D`anger att tidsperioden är tre dagar lång.

Följande yttrande och dess partiella JSON-svar visas nedan.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3-svar](#tab/3-1)

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


#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/3-2)

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

#### <a name="v2-response"></a>[V2-svar](#tab/3-3)

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exempel på datumintervallmatchning för veckodag

I följande exempel visas hur LUIS använder **datetimeV2** för att lösa uttrycket `Tuesday to Thursday`. I det här exemplet är det aktuella datumet den 19 juni. LUIS innehåller **daterange-värden** för båda datumintervallen som föregår och följer det aktuella datumet.

Följande yttrande och dess partiella JSON-svar visas nedan.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3-svar](#tab/4-1)

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

#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/4-2)

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

#### <a name="v2-response"></a>[V2-svar](#tab/4-3)

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
Värdematrisen har två tidselement om tiden eller tidsintervallet är tvetydigt. När det finns en tvetydig tid, värden har både A.M. och p.m. Gånger.

## <a name="time-range-resolution-example"></a>Exempel på tidsintervallupplösning

DatetimeV2 JSON-svaret har ändrats i API V3. I följande exempel visas hur LUIS använder **datetimeV2** för att lösa uttryck som har ett tidsintervall.

Ändringar från API V2:
* `datetimeV2.timex.type`returneras inte längre eftersom den returneras på `datetimev2.type`den överordnade nivån .
* Egenskapen `datetimeV2.value` har bytt `datetimeV2.timex`namn till .

Följande yttrande och dess partiella JSON-svar visas nedan.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3-svar](#tab/5-1)

Följande JSON är `verbose` med parametern inställd `false`på:

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
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/5-2)

Följande JSON är `verbose` med parametern inställd `true`på:

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
#### <a name="v2-response"></a>[V2-svar](#tab/5-3)

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

## <a name="time-resolution-example"></a>Exempel på tidsupplösning

Följande yttrande och dess partiella JSON-svar visas nedan.

`8am`

#### <a name="v3-response"></a>[V3-svar](#tab/6-1)

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
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/6-2)

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
#### <a name="v2-response"></a>[V2-svar](#tab/6-3)

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

## <a name="deprecated-prebuilt-datetime"></a>Föråldrad fördefinierad datetime

Den `datetime` fördefinierade entiteten är föråldrad och ersätts av **datetimeV2**.

Gör `datetime` så `datetimeV2` här om du vill ersätta med i LUIS-appen:

1. Öppna fönstret **Entiteter** i webbgränssnittet LUIS.
2. Ta bort den fördefinierade entiteten **datetime.**
3. Klicka på **Lägg till fördefinierad entitet**
4. Välj **datetimeV2** och klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [dimensionen,](luis-reference-prebuilt-dimension.md) [e-postentiteterna](luis-reference-prebuilt-email.md) och [numret](luis-reference-prebuilt-number.md).

