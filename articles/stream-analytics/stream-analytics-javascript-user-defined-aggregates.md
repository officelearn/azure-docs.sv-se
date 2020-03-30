---
title: JavaScript-användardefinierade aggregat i Azure Stream Analytics
description: I den här artikeln beskrivs hur du utför avancerad frågemekanik med JavaScript-användardefinierade aggregat i Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531742"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>JavaScript-användardefinierade aggregat i Azure Stream Analytics
 
Azure Stream Analytics stöder användardefinierade aggregat (UDA) skrivna i JavaScript, det gör att du kan implementera komplex tillståndskänslig affärslogik. Inom UDA har du full kontroll över tillståndsdatastrukturen, tillståndsackumulering, tillståndsavstulering och aggregerad resultatberäkning. Artikeln introducerar de två olika JavaScript UDA-gränssnitten, stegen för att skapa en UDA och hur du använder UDA med fönsterbaserade åtgärder i Stream Analytics-frågan.

## <a name="javascript-user-defined-aggregates"></a>JavaScript användardefinierade aggregat

En användardefinierad mängd används ovanpå en tidsfönsterspecifikation för att aggregera över händelserna i det fönstret och producera ett enda resultatvärde. Det finns två typer av UDA-gränssnitt som Stream Analytics stöder idag, AccumulateOnly och AccumulateDeaccumulate. Båda typerna av UDA kan användas av Tumlande, Hopping, Sliding och Session Window. AccumulateDeaccumulate UDA presterar bättre än AccumulateOnly UDA när den används tillsammans med Hopping, Sliding och Session Window. Du väljer en av de två typerna baserat på den algoritm du använder.

### <a name="accumulateonly-aggregates"></a>AckumulerasEnast aggregat

AckumulerasEnastlig mängd kan bara ackumulera nya händelser till sitt tillstånd, algoritmen tillåter inte deaccumulation av värden. Välj den här mängdtypen när det är omöjligt att ange en händelseinformation från tillståndsvärdet. Följande är JavaScript-mallen för AccumulatOnly-aggregat:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AckumuleraDeaccumulate aggregat

AckumuleradeDeaccumulate-aggregat tillåter deaccumulation av ett tidigare ackumulerat värde från tillståndet, till exempel ta bort ett nyckelvärdespar från en lista över händelsevärden eller subtrahera ett värde från ett beloppstillstånd. Följande är JavaScript-mallen för AccumulateDeaccumulate-aggregat:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript funktionsdeklaration

Varje JavaScript UDA definieras av en funktionsobjektdeklaration. Följande är de viktigaste inslagen i en UDA definition.

### <a name="function-alias"></a>Funktionsalias

Funktionsalias är UDA-identifieraren. När anropas i Stream Analytics-frågan ska du alltid använda UDA-alias tillsammans med ett "uda". .

### <a name="function-type"></a>Funktionstyp

För UDA ska funktionstypen vara **Javascript UDA**.

### <a name="output-type"></a>Utdatatyp

En specifik typ som Stream Analytics-jobbet stöds, eller "Any" om du vill hantera typen i frågan.

### <a name="function-name"></a>Funktionsnamn

Namnet på det här funktionsobjektet. Funktionsnamnet ska matcha UDA-aliaset.

### <a name="method---init"></a>Metod - init()

Metoden init() initierar tillståndet för aggregatet. Den här metoden anropas när fönstret startar.

### <a name="method--accumulate"></a>Metod – ackumulerad()

Metoden accumulate() beräknar UDA-tillståndet baserat på föregående tillstånd och aktuella händelsevärden. Den här metoden anropas när en händelse kommer in i ett tidsfönster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW eller SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metod – avaccumulate()

Metoden deaccumulate() beräknar om tillståndet baserat på föregående tillstånd och aktuella händelsevärden. Den här metoden anropas när en händelse lämnar en SLIDINGWINDOW eller SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metod – deaccumulateState()

Metoden deaccumulateState() beräknar om tillståndet baserat på föregående tillstånd och tillståndet för ett hopp. Den här metoden anropas när en uppsättning händelser lämnar en HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metod – computeResult()

Metoden computeResult() returnerar aggregerat resultat baserat på det aktuella tillståndet. Den här metoden anropas i slutet av ett tidsfönster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW eller SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA-stödda datatyper för in- och utdata som stöds
För JavaScript UDA-datatyper finns i avsnittet Konvertering av **typen Stream Analytics och JavaScript** för Integrera [JavaScript-UDF:er](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Lägga till ett JavaScript UDA från Azure-portalen

Nedan går vi igenom processen att skapa en UDA från Portal. Exemplet vi använder här är att beräkna tidsvägda medelvärden.

Nu ska vi skapa en JavaScript UDA under ett befintligt ASA-jobb genom att följa stegen.

1. Logga in på Azure-portalen och hitta ditt befintliga Stream Analytics-jobb.
1. Klicka sedan på funktioner länk under **JOBB TOPOLOGI**.
1. Klicka på ikonen **Lägg** till för att lägga till en ny funktion.
1. I vyn Ny funktion väljer du **JavaScript UDA** som funktionstyp, och sedan visas en standardmall för UDA i redigeraren.
1. Fyll i "TWA" som UDA-alias och ändra funktionsimplementeringen som följande:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. När du klickar på "Spara"-knappen visas din UDA på funktionslistan.

1. Klicka på den nya funktionen "TWA", kan du kontrollera funktionsdefinitionen.

## <a name="calling-javascript-uda-in-asa-query"></a>Anropa JavaScript UDA i ASA-fråga

I Azure-portalen och öppna jobbet redigerar du funktionen fråga och anropa TWA() med ett mandatprefix "uda". Ett exempel:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testa fråga med UDA

Skapa en lokal JSON-fil med innehållet nedan, ladda upp filen till Stream Analytics-jobbet och testa ovanstående fråga.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Få hjälp

Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Azure Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [AZURE Stream Analytics-hanterings-REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
