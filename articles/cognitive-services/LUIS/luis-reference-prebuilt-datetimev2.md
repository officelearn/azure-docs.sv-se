---
title: Den färdiga THOMAS entiteter datetimeV2 referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln har datetimeV2 färdiga entitetsinformation i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 261f6f27c39c280efdcd070888d735374a473c85
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321892"
---
# <a name="datetimev2-entity"></a>DatetimeV2 entitet

Den **datetimeV2** färdiga entitet hämtar värden för datum och tid. Dessa värden matcha ett standardiserat format för klientprogram kan använda. När en utterance har ett datum eller klockslag som inte är klar, THOMAS innehåller _både tidigare och framtida värden_ endpoint-svar. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller datetimeV2 till programmet avsikter. 

## <a name="types-of-datetimev2"></a>Typer av datetimeV2
DatetimeV2 hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) Github-lagringsplatsen

## <a name="example-json"></a>Exempel JSON 
I följande exempel JSON-svar har en `datetimeV2` entitet med en undertyp till `datetime`. Exempel på andra typer av datetimeV2 enheter finns [undertyper av datetimeV2](#subtypes-of-datetimev2)</a>.

```JSON
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

## <a name="json-property-descriptions"></a>JSON-Egenskapsbeskrivningar

|Egenskapsnamn |Egenskapstypen och beskrivning|
|---|---|
|Entitet|**strängen** -Text som hämtats från utterance med typen av datum, tid, datumintervall eller tidsintervall.|
|typ|**strängen** – en av de [undertyper av datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** -index i utterance som entiteten börjar.|
|endIndex|**int** -index i utterance som entiteten slutar.|
|lösning|Har en `values` matris som har en, två eller fyra [värdena för matchning av](#values-of-resolution).|
|slut|Slutvärdet för en tid eller datumintervall i samma format som `value`. Används endast om `type` är `daterange`, `timerange`, eller `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Undertyper av datetimeV2

Den **datetimeV2** färdiga entiteten har följande undertyper och exempel på varje finns i tabellen nedan:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Värdena för upplösning
* Matrisen innehåller ett element om datum och tid i utterance är helt angivna och entydigt.
* Matrisen innehåller två element om datetimeV2-värdet är tvetydig. Tvetydighet innehåller bristande visst år, tid eller tidsintervall. Se [tvetydiga datum](#ambiguous-dates) exempel. När tiden är tvetydigt för am. eller P.M., båda värdena ingår.
* Matrisen innehåller fyra element om utterance innehåller två element med tvetydighet. Den här tvetydigheten innehåller element som har:
  * Ett datum eller datumintervall är tvetydig på år
  * En tid eller tidsintervall som är tvetydig om klockan eller PM Exempel 3:00 den 3 April.

Varje element i den `values` matris kan ha följande fält: 

|Egenskapsnamn|Egenskapsbeskrivning|
|--|--|
|Timex|tid, datum eller datumintervall uttryckt TIMEX-format som inte följer den [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601) och TIMEX3 attribut för anteckningens med TimeML språket. Den här anteckningen beskrivs i den [TIMEX riktlinjer](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|typ|Undertyp som kan vara något av följande objekt: datetime, datum, tid, daterange, timerange, datetimerange, varaktighet, uppsättningen.|
|värde|**Valfritt.** Ett datetime-objekt i formatet yyyy:MM:dd (datum): mm: SS (time) yyyy:MM:dd: mm: SS (datetime). Om `type` är `duration`, värdet är antalet sekunder (varaktighet) <br/> Används endast om `type` är `datetime` eller `date`, `time`, eller ”varaktighet.|

## <a name="valid-date-values"></a>Giltiga värden

Den **datetimeV2** stöder datumen mellan följande intervall:

| Min | Max |
|----------|-------------|
| 1 januari 1900   | den 31 December 2099 |

## <a name="ambiguous-dates"></a>Tvetydiga datum

Om datumet kan vara i förflutna eller framtida, THOMAS innehåller båda värdena. Ett exempel är en utterance som innehåller månaden och dagen utan året.  

Till exempel få utterance ”maj 2”:
* Om dagens datum är kan 3 2017, innehåller THOMAS både ”2017-05-02” och ”2018-05-02” som värden. 
* Om dagens datum är kan 1 2017, innehåller THOMAS både ”2016-05-02” och ”2017-05-02” som värden.

I följande exempel visas lösning av entiteten ”maj 2”. Denna lösning förutsätter att dagens datum är ett datum mellan maj 2 2017 och kan 1 2018.
Fält med `X` i den `timex` fältet är delar av datumet som inte uttryckligen anges i utterance.

```JSON
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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum intervallet upplösning exempel numeriska datum

Den `datetimeV2` entiteten extraherar intervall för datum och tid. Den `start` och `end` anger början och slutet av intervallet. För utterance ”maj 2 till 5 maj”, THOMAS ger **daterange** värden för både det aktuella året och nästa år. I den `timex` fältet den `XXXX` värden anger tvetydighet på året. `P3D` Anger hur lång tid som är tre dagar lång.

```JSON
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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum intervallet upplösning exempel veckodag

I följande exempel visas hur THOMAS använder **datetimeV2** att lösa utterance ”tisdag till torsdag”. I det här exemplet är det aktuella datumet 19 juni. THOMAS innehåller **daterange** värden för båda datumintervall som före och efter det aktuella datumet.

```JSON
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
Värden matrisen har två tidselement om tid eller tidsintervallet är tvetydig. När det är en tvetydig, har värden båda kl. och. gånger.

## <a name="time-range-resolution-example"></a>Exempel på tid intervallet namnmatchning

I följande exempel visas hur THOMAS använder **datetimeV2** att matcha utterance som har ett tidsintervall.

```
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

## <a name="deprecated-prebuilt-datetime"></a>Föråldrad färdiga datetime

Den `datetime` färdiga entiteten är föråldrad och ersättas med [ `datetimeV2` ](#builtindatetimev2). 

Ersätt `datetime` med `datetimeV2` i appen THOMAS gör du följande:

1. Öppna den **entiteter** rutan THOMAS webbgränssnitt. 
2. Ta bort den **datetime** färdiga entitet.
3. Klicka på **lägga till färdiga entitet**
4. Välj **datetimeV2** och på **spara**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [dimension](luis-reference-prebuilt-dimension.md), [e-post](luis-reference-prebuilt-email.md) entiteter, och [nummer](luis-reference-prebuilt-number.md). 

