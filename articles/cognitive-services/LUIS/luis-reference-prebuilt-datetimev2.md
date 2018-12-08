---
title: DatetimeV2 fördefinierade entiteter
titleSuffix: Azure
description: Den här artikeln har datetimeV2 fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: f59c429906d6cee2b9f4aff6e16ba23e8d28c97b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105860"
---
# <a name="datetimev2-entity"></a>DatetimeV2-entitet

Den **datetimeV2** fördefinierade entitet extraherar värdena för datum och tid. Dessa värden lösa i ett standardformat för klientprogram kan använda. När ett uttryck har datum och tid som inte är klar, LUIS innehåller _både tidigare och framtida värden_ reaktion slutpunkt. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller datetimeV2 till programmet avsikter. 

## <a name="types-of-datetimev2"></a>Typer av datetimeV2
DatetimeV2 hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) Github-lagringsplats

## <a name="example-json"></a>Exempel på JSON 
I följande exempel JSON-svar finns en `datetimeV2` entitet med en undertyp till `datetime`. Exempel på andra typer av datetimeV2 entiteter finns [undertyper till datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Beskrivningar av JSON-egenskapen

|Egenskapsnamn |Egenskapstypen och beskrivning|
|---|---|
|Entitet|**sträng** -Text som extraherats från uttryck med typen av datum, tid, datum eller tidsintervall.|
|typ|**sträng** – en av de [undertyper till datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** -index i uttryck då entiteten börjar.|
|endIndex|**int** -index i uttryck då entiteten har upphört.|
|lösning|Har en `values` matris som har en, två eller fyra [värdena för lösning](#values-of-resolution).|
|slut|Slutvärdet för en tid eller ett intervall, i samma format som `value`. Används endast om `type` är `daterange`, `timerange`, eller `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Undertyper till datetimeV2

Den **datetimeV2** fördefinierade entitet har följande undertyper och exempel på var och en finns i tabellen nedan:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

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
|typ|Undertypen, som kan vara något av följande objekt: datetime, datum, tid, daterange, timerange, datetimerange, varaktighet, set.|
|värde|**Valfritt.** Ett datetime-objekt i formatet yyyy:MM:dd (datum): mm: SS (tid) yyyy:MM:dd: mm: SS (datetime). Om `type` är `duration`, värdet är antalet sekunder (varaktighet) <br/> Används endast om `type` är `datetime` eller `date`, `time`, eller ”varaktighet.|

## <a name="valid-date-values"></a>Ogiltigt datum

Den **datetimeV2** stöder datumen mellan följande intervall:

| Min | Max |
|----------|-------------|
| den 1 januari 1900   | den 31 December 2099 |

## <a name="ambiguous-dates"></a>Tvetydiga datum

Om datumet kan vara i förflutna eller framtida, LUIS innehåller båda värdena. Ett exempel är ett uttryck som innehåller månad och datum utan året.  

Till exempel få uttryck ”maj 2nd”:
* Om dagens datum är den 3 2017, innehåller LUIS både ”2017-05-02” och ”2018-05-02” som värden. 
* När dagens datum är den 1 maj 2017, innehåller LUIS både ”2016-05-02” och ”2017-05-02” som värden.

I följande exempel visas av lösningen på entitet som är ”maj 2”. Denna lösning förutsätter att dagens datum är ett datum mellan den 2 2017 och den 1 2018.
Fält med `X` i den `timex` fält är delar av datumet som inte uttryckligen anges i uttryck.

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
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum intervallet upplösning exempel för numeriska datum

Den `datetimeV2` entiteten extraherar datum- och tidsintervall. Den `start` och `end` anger början och slutet av intervallet. För uttryck ”2 maj till 5 maj”, THOMAS ger **daterange** värden för både det aktuella året och nästa år. I den `timex` fält, de `XXXX` värden anger tvetydighet på året. `P3D` Anger hur lång tid är tre dagar långa.

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
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum intervallet upplösning exempel för dag i veckan

I följande exempel visas hur LUIS använder **datetimeV2** att lösa uttryck ”tisdag till torsdag”. I det här exemplet är det aktuella datumet 19 juni. LUIS innehåller **daterange** värden för båda datumintervall som före och efter det aktuella datumet.

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
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Tvetydig tid
Värden matrisen har två tidselement om den tid, eller tidsintervallet är tvetydig. Om det finns en tvetydig tid, har värden båda kl. och. gånger.

## <a name="time-range-resolution-example"></a>Exempel på tid intervallet namnmatchning

I följande exempel visas hur LUIS använder **datetimeV2** att lösa det uttryck som har ett tidsintervall.

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

## <a name="deprecated-prebuilt-datetime"></a>Föråldrad fördefinierade datum/tid

Den `datetime` fördefinierade entiteten är inaktuell och ersatts av **datetimeV2**. 

Ersätt `datetime` med `datetimeV2` i din LUIS-app, gör du följande:

1. Öppna den **entiteter** rutan i LUIS-webbgränssnittet. 
2. Ta bort den **datetime** fördefinierade entitet.
3. Klicka på **Lägg till fördefinierade entitet**
4. Välj **datetimeV2** och klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [dimension](luis-reference-prebuilt-dimension.md), [e-post](luis-reference-prebuilt-email.md) entiteter, och [nummer](luis-reference-prebuilt-number.md). 

