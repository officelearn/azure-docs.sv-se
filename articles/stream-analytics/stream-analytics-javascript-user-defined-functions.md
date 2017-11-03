---
title: "Azure Stream Analytics JavaScript användardefinierade funktioner | Microsoft Docs"
description: "Utföra en avancerad fråga säkerhetsnivån med JavaScript användardefinierade funktioner"
keywords: "JavaScript, användardefinierade funktioner, udf"
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: e8c1c784a598416b478d1430258201053185fdee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics JavaScript användardefinierade funktioner
Azure Stream Analytics stöder användardefinierade funktioner som skrivits i JavaScript. Med omfattande uppsättning **sträng**, **RegExp**, **matematiska**, **matris**, och **datum** metoder som JavaScript innehåller, komplexa Datatransformationer med Stream Analytics-jobb blir det lättare att skapa.

## <a name="javascript-user-defined-functions"></a>JavaScript användardefinierade funktioner
JavaScript användardefinierade funktioner stöder tillståndslösa, endast beräkning skalärfunktioner som inte kräver extern anslutning. Returvärdet för en funktion kan endast vara ett skalärvärde (enkel). När du lägger till en JavaScript-användardefinierad funktion ett jobb kan använda du funktionen var som helst i frågan som en inbyggd skalärfunktion.

Här följer några scenarier där användbara JavaScript användardefinierade funktioner:
* Tolkning och manipulera strängar som har funktioner för reguljära uttryck, till exempel **Regexp_Replace()** och **Regexp_Extract()**
* Avkoda och kodning data, till exempel binary-hex-konvertering
* Utföra matematiska beräkningar med JavaScript **matematiska** funktioner
* Utför åtgärder i matrisen som sortera, koppling, Sök och fill

Här följer några saker som du inte kan göra med en JavaScript-användardefinierad funktion i Stream Analytics:
* Anropet ut externa REST-slutpunkter, till exempel utför omvänd sökning av IP- eller datahämtning referensdata från en extern källa
* Utför anpassade händelsen format serialisering eller avserialisering på indata/utdata
* Skapa anpassade mängder

Även om fungerar som **Date.GetDate()** eller **Math.random()** blockeras inte i definitionen funktioner du bör undvika att använda dem. Dessa funktioner **inte** tillbaka detta varje gång du anropar dem, och Azure Stream Analytics-tjänsten inte hålla en journal av funktionsanrop och returnerade resultat. Om en funktion returnerar olika resultat på samma händelser, garanteras inte repeterbarhet när ett jobb startas av dig eller Stream Analytics-tjänsten.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Lägg till JavaScript användardefinierad funktion i Azure-portalen
För att skapa en enkel JavaScript användardefinierad funktion under ett befintligt Stream Analytics-jobb, gör du följande:

1.  Hitta Stream Analytics-jobbet i Azure-portalen.
2.  Under **jobbet TOPOLOGI**, Välj din funktion. En tom lista över funktioner visas.
3.  Om du vill skapa en användardefinierad funktion, Välj **Lägg till**.
4.  På den **nya funktionen** bladet för **funktionstyp**väljer **JavaScript**. En standardmall för funktionen visas i redigeraren.
5.  För den **UDF alias**, ange **hex2Int**, och ändra implementeringen funktion på följande sätt:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Välj **Spara**. Funktionen visas i listan över funktioner.
7.  Välj den nya **hex2Int** fungerar och kontrollera funktionsdefinitionen. Alla funktioner har en **UDF** prefix som läggs till funktionen alias. Du behöver *prefixet* när du anropar funktionen i Stream Analytics-fråga. I så fall måste du anropa **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Anropa en användardefinierad JavaScript-funktion i en fråga

1. I frågeredigeraren för under **jobbet TOPOLOGI**väljer **frågan**.
2.  Redigera frågan och sedan anropa den användardefinierade funktionen så här:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Högerklicka på jobbet indata för att ladda upp data exempelfilen.
4.  Om du vill testa frågan väljer **testa**.


## <a name="supported-javascript-objects"></a>Stöds JavaScript-objekt
Azure Stream Analytics JavaScript användardefinierade funktioner stöder standard, inbyggda JavaScript-objekt. En lista över de här objekten finns [globala objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics och JavaScript typkonvertering

Det finns skillnader i typer att Stream Analytics fråga språk och stöd för JavaScript. Den här tabellen innehåller mappningar mellan två konvertering:

Stream Analytics | JavaScript
--- | ---
bigint | Antal (JavaScript kan endast representera heltal upp till exakt 2 ^ 53)
Datum och tid | Datum (JavaScript endast stöder millisekunder)
dubbla | Tal
nvarchar(max) | Sträng
Post | Objekt
Matris | Matris
NULL | Null


Här är JavaScript-Stream Analytics-konvertering:


JavaScript | Stream Analytics
--- | ---
Tal | Bigint (om talet är runda och mellan lång. MinValue och lång. MaxValue; Annars är det dubbla)
Date | Datum och tid
Sträng | nvarchar(max)
Objekt | Post
Matris | Matris
Null, Odefinierad | NULL
En annan typ (till exempel en funktion eller fel) | Stöds inte (resulterar i körningsfel)

## <a name="troubleshooting"></a>Felsökning
JavaScript-körningsfel betraktas som allvarligt och är anslutna via aktivitetsloggen. För att hämta loggen i Azure portal, gå till jobbet och välj **aktivitetsloggen**.


## <a name="other-javascript-user-defined-function-patterns"></a>Andra JavaScript användardefinierad funktion mönster

### <a name="write-nested-json-to-output"></a>Skriva kapslade JSON till utdata
Om du har en uppföljning bearbetningssteg som använder en Stream Analytics-jobbet utdata som indata, och det krävs en JSON-format, kan du skriva en JSON-strängen till utdata. I nästa exempel anropar den **JSON.stringify()** för att packa alla namn/värde-par av indata, och sedan skriva dem som ett enda strängvärde i utdata.

**Definition av JavaScript användardefinierad funktion:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Exempelfråga:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Få hjälp
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language-referens](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
