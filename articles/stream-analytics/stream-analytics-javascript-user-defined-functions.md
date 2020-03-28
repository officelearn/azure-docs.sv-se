---
title: Användardefinierade funktioner i Azure Stream Analytics JavaScript
description: Den här artikeln är en introduktion till JavaScript-användardefinierade funktioner i Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235401"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>JavaScript-användardefinierade funktioner i Azure Stream Analytics
 
Azure Stream Analytics stödjer användardefinierade JavaScript-funktioner. Med den omfattande uppsättningen **string-,** **RegExp-,** **matematiska, array-** och **datummetoder** som JavaScript tillhandahåller blir komplexa dataomvandlingar med Stream Analytics-jobb enklare att skapa. **Math**

## <a name="overview"></a>Översikt

JavaScript-användardefinierade funktioner stöder tillståndslösa skalärfunktioner som endast är beräkningsbara och som inte kräver extern anslutning. Returvärdet för en funktion kan endast vara ett skalärvärde (enkelt). När du lägger till en användardefinierad JavaScript-funktion i ett jobb kan använda funktionen var som helst i frågan, som en inbyggd skalärfunktion.

Här följer några scenarier där användardefinierade JavaScript-funktioner kan vara användbara:
* Tolka och manipulera strängar som har funktioner med reguljära uttryck, till exempel **Regexp_Replace()** och **Regexp_Extract()**
* Avkoda och koda data, till exempel konvertering av binärt format till hexadecimalt
* Gör matematiska beräkningar med JavaScript **Math-funktioner**
* Utföra matrisåtgärder som sortera, gå med, hitta och fylla

Här är några saker du inte kan göra med en JavaScript-användardefinierad funktion i Stream Analytics:
* Anropa externa REST-slutpunkter, till exempel göra omvänd IP-sökning eller hämta referensdata från en extern källa
* Utför anpassade serialisering eller avserialisering av händelseformat på indata/utdata
* Skapa anpassade samlingar

Även om funktioner som **Date.GetDate()** eller **Math.random()** inte blockeras i funktionsdefinitionen bör du undvika att använda dem. Dessa funktioner returnerar **inte** samma resultat varje gång du anropar dem, och Azure Stream Analytics-tjänsten för inte en journal över funktionsanrop och returnerade resultat. Om en funktion returnerar olika resultat på samma händelser garanteras inte repeterbarhet när ett jobb startas om av dig eller av Tjänsten Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Lägga till en JavaScript-användardefinierad funktion i jobbet

> [!NOTE]
> De här stegen fungerar med Stream Analytics-jobb som konfigurerats för att köras i molnet. Om ditt Stream Analytics-jobb är konfigurerat för att köras på Azure IoT Edge använder du i stället Visual Studio och [skriver den användardefinierade funktionen med C#](stream-analytics-edge-csharp-udf.md).

Om du vill skapa en JavaScript-användardefinierad funktion i ditt Stream Analytics-jobb väljer du **Funktioner** under **Jobbtopologi**. Välj sedan **JavaScript UDF** på rullgardinsmenyn **+Lägg till.** 

![Lägg till JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Du måste sedan ange följande egenskaper och välja **Spara**.

|Egenskap|Beskrivning|
|--------|-----------|
|Funktionsalias|Ange ett namn som ska anropa funktionen i frågan.|
|Utdatatyp|Typ som returneras av din JavaScript-användardefinierade funktion till din Stream Analytics-fråga.|
|Funktionsdefinition|Implementering av javascript-funktionen som ska köras varje gång din UDF anropas från din fråga.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testa och felsöka JavaScript-UDF:er 

Du kan testa och felsöka din JavaScript UDF-logik i alla webbläsare. Felsökning och testning av logiken för dessa användardefinierade funktioner stöds för närvarande inte i Stream Analytics-portalen. När funktionen fungerar som förväntat kan du lägga till den i Stream Analytics-jobbet som nämnts ovan och sedan anropa den direkt från din fråga. Du kan testa frågelogiken med JavaScript UDF med Hjälp av [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

JavaScript-körningsfel betraktas som allvarliga och exponeras via aktivitetsloggen. För att hämta loggen i Azure Portal går du till jobbet och väljer **aktivitetsloggen**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Anropa en användardefinierad JavaScript-funktion i en fråga

Du kan enkelt anropa javascript-funktionen i frågan med hjälp av funktionen alias föregås med **udf**. Här är ett exempel på en JavaScript UDF som konverterar hexadecimala värden till heltal som anropas i en Stream Analytics-fråga.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>JavaScript-objekt som stöds

Användardefinierade JavaScript-funktioner i Azure Stream Analytics stödjer inbyggda standardobjekt i JavaScript. En lista över de här objekten finns i [Globala objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics och JavaScript typkonvertering

Det finns skillnader i vilka typer som Stream Analytics frågespråk och JavaScript stödjer. Den här tabellen visar konverteringsmappningar mellan dessa:

Stream Analytics | JavaScript
--- | ---
bigint | Siffra (JavaScript kan bara representera heltal upp till exakt 2^53)
DateTime | Datum (JavaScript stöder endast millisekunder)
double | Tal
nvarchar(MAX) | String
Spela in | Objekt
Matris | Matris
NULL | Null

Här är konverteringarna från JavaScript till Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Tal | Bigint (om talet är avrundat och mellan long.MinValue och long.MaxValue, i annat fall stöds det inte)
Datum | DateTime
String | nvarchar(MAX)
Objekt | Spela in
Matris | Matris
Null, odefinierad | NULL
Annan typ (till exempel en funktion eller fel) | Stöds inte (resulterar i körningsfel)

JavaScript-språket är skiftlägeskänsligt och höljet av objektfälten i JavaScript-koden måste matcha höljet för fälten i inkommande data. Jobb med kompatibilitetsnivå 1.0 konverterar fält från SQL SELECT-uttrycket till gemener. Under kompatibilitetsnivå 1.1 och högre har fält från SELECT-uttrycket samma hölje som anges i SQL-frågan.

## <a name="other-javascript-user-defined-function-patterns"></a>Andra mönster för användardefinierade JavaScript-funktioner

### <a name="write-nested-json-to-output"></a>Skriva kapslad JSON till utdata

Om du har ett bearbetningssteg för uppföljning som använder Stream Analytics-jobbets utdata som indata, och det kräver ett JSON-format, kan du skriva en JSON-sträng till utdata. I nästa exempel anropar den funktionen **JSON.stringify()** för att packa alla namn-/värdepar för indata och sedan skriva dem som ett enda strängvärde i utdata.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Exempelfråga:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>Nästa steg

* [Maskininlärning UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
