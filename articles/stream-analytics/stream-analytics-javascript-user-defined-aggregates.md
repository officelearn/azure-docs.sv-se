---
title: Användardefinierade JavaScript-aggregeringar i Azure Stream Analytics
description: Den här artikeln beskriver hur du utför avancerade frågor med användardefinierade JavaScript-aggregeringar användardefinierade i Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6663e3fc48408de83e92f39e8c8070005818852d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479566"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript-användardefinierade aggregeringar (förhandsversion)
 
Azure Stream Analytics stöder användardefinierade aggregeringar (UDA) skriven i JavaScript, du kan implementera komplex tillståndskänsliga affärslogik. Inom UDA har du fullständig kontroll över tillstånd datastrukturen, tillstånd anhopning, detta tillstånd och aggregerade resultat beräkning. Artikeln introducerar de två olika JavaScript UDA-gränssnitt, steg för att skapa en UDA och hur du använder UDA med Windows-baserad åtgärder i Stream Analytics-fråga.

## <a name="javascript-user-defined-aggregates"></a>JavaScript-användardefinierade aggregeringar

En användardefinierad samling används ovanpå en tidsangivelse för fönstret för att sammanställa via händelser i fönstret och producera ett enskilt resultatvärde. Det finns två typer av UDA-gränssnitt att Stream Analytics stöder idag, AccumulateOnly och AccumulateDeaccumulate. Båda typerna av UDA kan användas av rullande fönster, hoppar fönster och glidande fönster. AccumulateDeaccumulate UDA presterar bättre än AccumulateOnly UDA när de används tillsammans med hoppar fönster och glidande fönster. Du väljer ett av de två typerna som baseras på den algoritm som du använder.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly aggregeringar

AccumulateOnly aggregeringar ackumulera endast nya händelser till dess tillstånd, algoritmen tillåter inte deaccumulation av värden. Välj den här typen av sammanställda deaccumulate när en händelse information från statusvärdet är omöjligt att implementera. Följande är JavaScript-mallen för AccumulatOnly aggregeringar:

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

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate aggregeringar

Tillåt deaccumulation av en tidigare ackumulerade värdet från tillstånd, till exempel AccumulateDeaccumulate aggregeringar, ta bort ett nyckel / värde-par från en lista med värden för event eller subtrahera ett värde från ett tillstånd där sammanställd summan. Följande är JavaScript-mallen för AccumulateDeaccumulate aggregeringar:

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

## <a name="uda---javascript-function-declaration"></a>UDA - deklarationen för JavaScript-funktion

Varje JavaScript UDA definieras av en funktionsdeklarationen för objektet. Följande är viktiga element i en definition av UDA.

### <a name="function-alias"></a>Funktionsalias

Funktionsalias är UDA-identifierare. När den anropas i Stream Analytics-fråga alltid använda UDA alias tillsammans med en ”uda”. prefix.

### <a name="function-type"></a>Funktionstyp

UDA, vilken funktion som ska vara **Javascript UDA**.

### <a name="output-type"></a>Utdatatyp

En specifik skriver den Stream Analytics-jobb som stöds eller ”alla” om du vill hantera den i frågan.

### <a name="function-name"></a>Funktionsnamn

Namnet på den här funktionen-objektet. Funktionsnamnet bokstavligen ska matcha UDA-alias (Förhandsgranska beteende och vi funderar på att anonym stödfunktionen när GA).

### <a name="method---init"></a>Metoden - init()

Metoden init() initierar status för samlingen. Den här metoden anropas när Windows startar.

### <a name="method--accumulate"></a>Metoden – accumulate()

Metoden accumulate() beräknar UDA tillståndet baserat på det tidigare tillståndet och de aktuella värdena för händelsen. Den här metoden anropas när en händelse som anger ett tidsfönster (TUMBLINGWINDOW, HOPPINGWINDOW eller SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoden – deaccumulate()

Metoden deaccumulate() beräknar om tillstånd baserat på det tidigare tillståndet och de aktuella värdena för händelsen. Den här metoden anropas när en händelse lämnar en SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoden – deaccumulateState()

Metoden deaccumulateState() beräknar om tillstånd baserat på föregående tillstånd och status för ett hopp. Den här metoden anropas när en uppsättning händelser lämna en HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoden – computeResult()

Metoden computeResult() returnerar aggregerade resultat som baseras på det aktuella tillståndet. Den här metoden anropas i slutet av en tidsperiod (TUMBLINGWINDOW HOPPINGWINDOW och SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA inkommande och utgående datatyper som stöds
JavaScript UDA-datatyper finns i avsnittet **Stream Analytics och JavaScript typkonvertering** av [integrera JavaScript UDF: er](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Att lägga till JavaScript UDA från Azure portal

Nedan går vi igenom processen för att skapa en UDA från portalen. Exemplet använder vi här är databehandling tidsviktade genomsnitt.

Nu ska vi skapa JavaScript UDA under ett befintligt ASA-jobb genom att följa stegen.

1. Logga in på Azure-portalen och leta upp ditt befintliga Stream Analytics-jobb.
1. Klicka på länken funktioner under **JOBBTOPOLOGI**.
1. Klicka på den **Lägg till** ikon för att lägga till en ny funktion.
1. På den nya funktionen i vyn väljer **JavaScript UDA** som typ av funktionen ser en standardmall för UDA som visas i redigeraren.
1. Fyll i ”Nivågränsvärde” som UDA-alias och ändrar som följande:

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

1. När du klickar på knappen ”Spara” din UDA som visas i listan funktion.

1. Klicka på den nya funktionen ”Nivågränsvärde” kan du kontrollera definitionen.

## <a name="calling-javascript-uda-in-asa-query"></a>Anropa JavaScript UDA i ASA-fråga

I Azure-portalen och öppna ditt jobb, redigera frågan och anropa TWA() funktion med utförda prefixet ”uda”. Exempel:

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

## <a name="testing-query-with-uda"></a>Testa frågan med UDA

Skapa en lokal JSON-fil med nedan innehåll, överföra filen till Stream Analytics-jobb och testa ovanför fråga.

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
* [Frågespråksreferens för Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
