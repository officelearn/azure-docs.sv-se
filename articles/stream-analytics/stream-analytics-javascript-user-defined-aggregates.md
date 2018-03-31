---
title: Azure Stream Analytics JavaScript användardefinierade aggregeringar | Microsoft Docs
description: Utföra en avancerad fråga säkerhetsnivån med JavaScript användardefinierade aggregeringar
keywords: JavaScript, användardefinierade aggregeringar, uda
services: stream-analytics
author: minhe-msft
manager: santoshb
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/28/2017
ms.author: minhe
ms.openlocfilehash: 9f9a2e33f97fc90265933ee9941f2d02a92cbc4c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript användardefinierade aggregeringar (förhandsgranskning)

Azure Stream Analytics stöder användardefinierade aggregeringar (UDA) skriven i JavaScript, du kan implementera komplexa tillståndskänslig affärslogik. Ha fullständig kontroll över datastruktur tillstånd, tillstånd anhopning, detta tillstånd och sammanställd resultatet beräkning inom UDA. Artikeln introduceras de två olika JavaScript UDA-gränssnitt, steg för att skapa en UDA och hur du använder UDA med Windows-baserad åtgärder i Stream Analytics-fråga.

## <a name="javascript-user-defined-aggregates"></a>JavaScript användardefinierade aggregeringar

En användardefinierad funktion används ovanpå en tidsangivelse fönstret för att sammanställa över händelser i fönstret och skapa ett enskilt resultatvärde. Det finns två typer av UDA-gränssnitt att Stream Analytics stöder idag AccumulateOnly och AccumulateDeaccumulate. Båda typerna av UDA kan användas av rullande fönster, Hopping fönster och glidande fönster. AccumulateDeaccumulate UDA presterar bättre än AccumulateOnly UDA när de används tillsammans med Hopping fönster och glidande fönster. Du väljer ett av de två baserat på den algoritm som du använder.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly mängder

AccumulateOnly mängder ackumulera endast nya händelser till dess tillstånd, algoritmen tillåter inte deaccumulation av värden. Välj den här typen av sammanställda deaccumulate när en händelse information från värdet state är omöjligt att implementera. Följande är JavaScript-mall för AccumulatOnly mängder:

````JavaScript
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
````

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate mängder

Tillåt deaccumulation i ett tidigare ackumulerade värde från tillstånd, till exempel AccumulateDeaccumulate mängder, ta bort ett nyckel / värde-par från en lista över händelsevärden eller subtrahera ett värde från ett tillstånd för sum sammanställd. Följande är JavaScript-mall för AccumulateDeaccumulate mängder:

````JavaScript
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
````

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript funktionsdeklarationen

Varje JavaScript UDA definieras av en funktionsdeklarationen för objektet. Följande är viktiga element i en UDA-definition.

### <a name="function-alias"></a>Funktionsalias

Funktionen alias är UDA-identifierare. När den anropas i Stream Analytics-fråga alltid använda UDA alias tillsammans med ”uda”. prefix.

### <a name="function-type"></a>Funktionstyp

För UDA, funktionen typen ska vara **Javascript UDA**.

### <a name="output-type"></a>Utdatatyp

En specifik Skriv det Stream Analytics-jobbet som stöds eller ”alla” om du vill hantera den i frågan.

### <a name="function-name"></a>Funktionsnamn

Namnet på den här funktionen-objekt. Funktionsnamnet bokstavligt bör matcha UDA-alias (Förhandsgranska beteende, vi funderar på att anonym stödfunktionen när GA).

### <a name="method---init"></a>Metoden - init()

Metoden init() initierar tillståndet för mängdfunktionen. Den här metoden anropas när Windows startas.

### <a name="method--accumulate"></a>Metoden – accumulate()

Metoden accumulate() beräknar tillståndet UDA baserat på de aktuella händelsen värdena och det tidigare tillståndet. Den här metoden anropas när en händelse försätts i ett tidsfönster (TUMBLINGWINDOW, HOPPINGWINDOW eller SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoden – deaccumulate()

Metoden deaccumulate() beräknar tillstånd baserat på de aktuella händelsen värdena och det tidigare tillståndet. Den här metoden anropas när en händelse lämnar en SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoden – deaccumulateState()

Metoden deaccumulateState() beräknar tillstånd baserat på det tidigare tillståndet och tillståndet för ett hopp. Den här metoden anropas när en uppsättning händelser lämna en HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoden – computeResult()

Metoden computeResult() returnerar sammanlagda resultat baserat på aktuell status. Den här metoden anropas i slutet av ett tidsfönster (TUMBLINGWINDOW HOPPINGWINDOW och SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA inkommande och utgående datatyper som stöds
JavaScript UDA-datatyper finns i avsnittet **Stream Analytics och JavaScript skriver konvertering** av [integrera JavaScript UDF: er](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Att lägga till en JavaScript UDA från Azure-portalen

Nedan går vi igenom processen att skapa en UDA från portalen. Exemplet använder vi här beräknar tid viktas medelvärden.

Nu skapar vi en JavaScript UDA under ett befintligt ASA jobb genom att följa steg.

1. Logga in på Azure-portalen och leta upp din befintliga Stream Analytics-jobbet.
1. Klicka på länken funktioner under **jobbet TOPOLOGI**.
1. Klicka på den **Lägg till** ikon för att lägga till en ny funktion.
1. På den nya funktionen i vyn väljer **JavaScript UDA** som typ av funktionen kan du se en standardmall för UDA som visas i redigeraren.
1. Fyll i ”Nivågränsvärde” som UDA-alias och ändra implementeringen för funktionen som följande:

    ````JavaScript
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
    ````

1. När du klickar på knappen ”Spara” visas din UDA i listan funktion.

1. Klicka på den nya funktionen ”Nivågränsvärde” du kan kontrollera funktionsdefinitionen.

## <a name="calling-javascript-uda-in-asa-query"></a>Anropar JavaScript UDA i ASA frågan

I Azure-portalen och öppna ditt jobb, redigera frågan och anropa TWA() funktion med förordnandet prefixet ”uda”. Exempel:

````SQL
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
````

## <a name="testing-query-with-uda"></a>Testa fråga med UDA

Skapa en lokal JSON-fil med nedan innehåll, överföra filen till Stream Analytics-jobbet och testa ovan frågan.

````JSON
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
````

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language-referens](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
