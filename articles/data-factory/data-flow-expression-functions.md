---
title: Uttrycks funktioner i funktionen för att mappa data flöde i Azure Data Factory
description: Lär dig mer om uttrycks funktioner i data flöde för data mappning.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: c062a75516a1b865c1ff6c35f00d4fbf7c4881c6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029371"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Data omvandlings uttryck i data flöde för mappning 



## <a name="expression-functions"></a>Uttrycks funktioner

I Data Factory använder du uttrycks språket i funktionen mappa data flöde för att konfigurera data transformationer.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Absolut värde för ett tal.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar ett värde för cosinus-inversen * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Lägger till ett par med strängar eller siffror. Lägger till ett datum i ett antal dagar. Lägger till en varaktighet i en tidsstämpel. Lägger till en matris av liknande typ till en annan. Samma som operatorn + * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Lägg till dagar i ett datum eller en tidsstämpel. Samma som +-operatorn för date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Lägg till månader i ett datum eller en tidsstämpel. Du kan också skicka en tidszon * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Logisk och-operator. Samma som & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Beräknar ett inverterat sinus-värde * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar ett inverterat tangens-värde * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar vinkeln i radianer mellan den positiva x-axeln i ett plan och den punkt som angetts av koordinaterna * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar medelvärdet för en kolumns värden * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor får du medelvärdet av värdena i en kolumn * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Markerar ett kolumn värde efter namn i data strömmen. Du kan skicka ett valfritt Stream-namn som det andra argumentet. Om det finns flera matchningar returneras den första matchningen. Om ingen matchning returneras ett NULL-värde. Det returnerade värdet måste vara en typ som konverteras av typ konverterings funktionerna (TO_DATE, TO_STRING...).  Kolumn namn som är kända i design tid ska bara åtgärdas med deras namn. Beräknade indata stöds inte, men du kan använda parameter ersättningar * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Markerar ett kolumn värde efter dess relativa position (1) i data strömmen. Om positionen ligger utanför intervallet returneras ett NULL-värde. Det returnerade värdet måste vara en typ som konverteras av typ konverterings funktionerna (TO_DATE, TO_STRING...) Beräknade indata stöds inte, men du kan använda parameter ersättningar * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Baserat på alternerande villkor används ett värde eller det andra. Om antalet indata är jämnt, är det andra att standardvärdet är NULL för det sista villkoret * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Beräkna kubnamnet för ett tal * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar det minsta heltal som inte är mindre än antalet * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar det första värdet som inte är null från en uppsättning indata. Alla indata ska vara av samma typ * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Jämför två värden av samma typ. Returnerar negativt heltal om värde1 < värde2, 0 om värde1 = = värde2, positivt värde om value1 > värde2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sammanfogar ett variabel antal strängar tillsammans. Samma som +-operatorn med strängar * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sammanfogar ett variabelt antal strängar tillsammans med en avgränsare. Den första parametern är avgränsaren * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Returnerar true om något element i den angivna matrisen utvärderas som sant i angivet predikat. Contains förväntar sig en referens till ett element i predikatet-funktionen som #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Beräknar ett cosinus-värde * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar ett hyperboliskt cosinus av ett värde * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar det sammanställda antalet värden. Om valfria kolumner anges, ignorerar den NULL-värden i antalet * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hämtar det sammanlagda antalet distinkta värden för en uppsättning kolumner * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtas det sammanställda antalet värden. Om den valfria kolumnen anges ignoreras NULL-värden i antalet * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar populationens kovarians mellan två kolumner * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar populationens kovarians för två kolumner * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar exempel kovariansen för två kolumner * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar exempel kovariansen för två kolumner * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar CRC32-hashen för en uppsättning av varierande primitiva data typer med en bit längd som bara kan vara av värdena 0 (256), 224, 256, 384, 512. Det kan användas för att beräkna ett finger avtryck för en rad * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
Funktionen CumeDist beräknar positionen för ett värde relativt till alla värden i partitionen. Resultatet är antalet rader före eller lika med den aktuella raden i ordningen för partitionen, dividerat med det totala antalet rader i fönstrets partition. Eventuella förbindelse värden i ordningen kommer att utvärderas till samma position.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Hämtar det aktuella datumet då jobbet börjar köras. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hämtar aktuell tidsstämpel när jobbet börjar köras med den lokala tids zonen * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar den aktuella tidsstämpeln som UTC. Om du vill att din aktuella tid ska tolkas i en annan tidszon än din kluster tids zon kan du skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den aktuella tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.htmlTo konvertera UTC-tiden till en annan tidszon använder fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hämtar dag i månaden angivet datum * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar vecko dagen med angivet datum. 1 – söndag, 2 – måndag..., 7-lördag * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar dag på året angivet datum * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Varaktighet i millisekunder för antalet dagar * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Konverterar radianer till grader * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar rangordningen för ett värde i en grupp med värden. Resultatet är ett plus antalet rader före eller lika med den aktuella raden i ordningen för partitionen. Värdena kommer inte att skapa luckor i sekvensen. Kompakt rang fungerar även när data inte sorteras och söker efter ändringar i värden * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Dividerar ett par med tal. Samma som operatorn * ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om strängen slutar med den angivna strängen * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Jämförelse likhets operator. Samma som = = operator * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Jämförelse är lika med operator som ignorerar Skift läge. Samma som < = > operatör * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Beräkna fakulteten för ett tal * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar alltid ett falskt värde. Använd Function-syntaxen (false ()) om det finns en kolumn med namnet "false" * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Filtrerar element utanför matrisen som inte uppfyller det angivna predikatet. Filter förväntar sig en referens till ett element i predikatet-funktionen som #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar det första värdet för en kolumn grupp. Om den andra parametern ignoreNulls utelämnas, antas det vara falskt * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Returnerar det största heltal som inte är större än antalet * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Konverterar till tidsstämpeln från UTC. Du kan välja att skicka timezone i formatet "GMT", "PST", "UTC", "America/Cayman". Standardvärdet är den aktuella timezoneRefer Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse av större operator. Samma som > operatör * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Jämförelse av större än eller lika operator. Samma som > = operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Returnerar det största värdet i listan med värden som indatamängden hoppar över null-värden. Returnerar null om alla indata är null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Söker efter ett kolumn värde efter namn i data strömmen. Du kan skicka ett valfritt Stream-namn som det andra argumentet.  Kolumn namn som är kända i design tid ska bara åtgärdas med deras namn. Beräknade indata stöds inte, men du kan använda parameter ersättningar * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar timme-värdet för en tidsstämpel. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Varaktighet i millisekunder för antalet timmar * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Mappar varje element i matrisen till ett nytt element med det angivna uttrycket. Kartan förväntar sig en referens till ett element i uttrycks funktionen som #item och en referens till element indexet som #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor gäller ett värde eller det andra. Om inget annat anges anses det vara NULL. Båda värdena måste vara kompatibla (numeriska, sträng...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kontrollerar om värdet inte är NULL och returnerar det andra. Den testar för alla indata tills den hittar det första värdet som inte är null * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kontrollerar om ett objekt finns i matrisen * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Konverterar den första bokstaven i varje ord till versaler. Ord identifieras som avgränsade med blank steg * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Söker efter under strängens position (1) i en sträng. 0 returneras om den inte hittas * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kontrollerar om raden har marker ATS för borttagning. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden har marker ATS som fel. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden har marker ATS för att ignoreras. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden har marker ATS för infogning. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden matchas vid sökning. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om värdet är NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden har marker ATS för uppdatering. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om raden har marker ATS för infogning. För omvandlingar som tar fler än en indataströmmen kan du skicka det (1-baserade) indexet för data strömmen. Standardvärdet för Stream-indexet är 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar en kolumns * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar du en kolumns * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar värdet för den första parametern utvärderade n rader före den aktuella raden. Den andra parametern är antalet rader som du vill se tillbaka och standardvärdet är 1. Om det inte finns lika många rader returneras värdet null om inte ett standardvärde anges * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar det sista värdet i en kolumn grupp. Om den andra parametern ignoreNulls utelämnas, antas det vara falskt * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar det sista datumet i månaden angivet datum * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar värdet för den första parametern utvärderade n rader efter den aktuella raden. Den andra parametern är antalet rader som ska se framåt och standardvärdet är 1. Om det inte finns lika många rader returneras värdet null om inte ett standardvärde anges * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Jämförelsen är mindre än eller lika med operatorn. Samma som < = operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Extraherar en under sträng som börjar vid index 1 med antalet tecken. Samma som del sträng (Str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Returnerar längden på strängen * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Jämförelse av mindre operator. Samma som < operatör * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Jämförelsen är mindre än eller lika med operatorn. Samma som < = operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar levenshtein avstånd mellan två strängar * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
Mönstret är en sträng som matchar bokstavligen. Undantagen är följande specialtecken: _ matchar valfritt tecken i indatatypen (liknar. i reguljära uttryck i POSIX matchar noll eller flera tecken i indatamängden (liknar. * i reguljära uttryck i POSIX).
Escape-tecken är. Om ett escape-tecken föregår en särskild symbol eller ett annat escape-tecken matchas följande tecken bokstavligen. Det är inte tillåtet att undanta andra tecken.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Söker efter positionen (1) för under strängen i en sträng som startar en viss position. Om positionen utelämnas beaktas den från början av strängen. 0 returneras om den inte hittas * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Beräknar logg värde. En valfri bas kan anges ytterligare ett Euler-nummer om det används * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar logg värde baserat på 10 bas * ``log10(100) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Gemener a String * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
Vänster fyller strängen med den angivna utfyllnaden tills den har en viss längd. Om strängen är lika med eller större än längden, trimmas den till längden * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' lpad (' Dumbo ', 8, ' < > ')-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
Left trimmar en sträng med inledande tecken. Om den andra parametern inte anges rensas blank steg. Annars trimmas alla bokstäver som anges i den andra parametern * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Mappar varje element i matrisen till ett nytt element med det angivna uttrycket. Kartan förväntar sig en referens till ett element i uttrycks funktionen som #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar det maximala värdet för en kolumn * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar det maximala värdet för en kolumn * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar MD5-sammandrag av en uppsättning av varierande primitiva data typer och returnerar en hex-sträng på 32 tecken. Det kan användas för att beräkna ett finger avtryck för en rad * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar medelvärdet för en kolumns värden. Samma som genomsnittlig * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtas medelvärdet av värden i en kolumn. Samma som avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar millisekund-värdet för ett datum. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Varaktighet i millisekunder för antalet millisekunder * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar det lägsta värdet för en kolumn * ``min(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar det lägsta värdet för en kolumn * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Subtraherar tal. Subtrahera från ett datum antal dagar. Substract varaktighet från en tidsstämpel. Substract två tidsstämplar för att få en skillnad i millisekunder. Samma som operatorn * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Hämtar minutens värde för en tidsstämpel. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Varaktighet i millisekunder för antal minuter * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Modulus av siffer par. Samma som% operator * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar månad svärdet för ett datum eller en tidsstämpel * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar antalet månader mellan två datum. Du kan avrunda beräkningen. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplicerar siffer paret. Samma som *-operatorn * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Funktionen NTile delar upp raderna för varje partition i `n` buckets från 1 till högst `n`. Bucket-värden kommer att skilja sig från de flesta 1. Om antalet rader i partitionen inte delas jämnt i antalet buckets, distribueras resten av värdena en per Bucket, från den första Bucket. Funktionen NTile är användbar för beräkning av tertiles, kvartilen, deciles och annan gemensam sammanfattnings statistik. Funktionen beräknar två variabler under initieringen: En vanlig Bucket-storlek kommer att ha en extra rad tillagd. Båda variablerna baseras på den aktuella partitionens storlek. Under beräknings processen håller funktionen reda på det aktuella rad numret, aktuellt Bucket-nummer och rad numret som Bucket ska ändras till (bucketThreshold). När det aktuella rad numret når Bucket-tröskelvärdet ökas värdet för Bucket med ett och tröskelvärdet ökas med Bucket-storlek (plus ett extra om den aktuella Bucket är utfylld).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negerar ett tal. Ändrar positiva värden till negativ och vice versa * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar nästa unika sekvens. Antalet är i följd bara inom en partition och har prefixet partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
Normalisera strängvärdet för att separera Unicode-tecken med accenttecken * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Logisk negation operator * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Jämförelse operatorn är inte lika med. Samma som! = operator * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Returnerar ett NULL-värde. Använd Function-syntaxen (null ()) om det finns en kolumn med namnet null. Alla åtgärder som använder resulterar i ett NULL-* ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Logisk OR-operator. Samma som | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Positiv Modulus av siffer par.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Returnerar aktuellt partitions-ID indatamängden är i * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Höjer ett tal till kraften hos en annan * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar rangordningen för ett värde i en grupp med värden. Resultatet är ett plus antalet rader före eller lika med den aktuella raden i ordningen för partitionen. Värdena kommer att skapa luckor i sekvensen. Rang fungerar även när data inte sorteras och söker efter ändring i värden * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Ackumulerar element i en matris. Om du minskar förväntas en referens till ett ackumulator värde och ett element i den första uttrycks funktionen som #acc och #item och det förväntar sig att det resulterande värdet är #result som ska användas i den andra uttrycks funktionen * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Extrahera en matchande del sträng för ett angivet regex-mönster. Den sista parametern identifierar matchnings gruppen och standardvärdet är 1 om den utelämnas. Använd <regex> (citat tecken) om du vill matcha en sträng utan att använda undantags tecken * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kontrollerar om strängen matchar det aktuella regex-mönstret. Använd <regex> (citat tecken) om du vill matcha en sträng utan att använda undantags tecken * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Ersätt alla förekomster av ett regex-mönster med en annan del sträng i den här strängen Använd <regex> (citat tecken) om du vill matcha en sträng utan att använda undantags tecken * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Delar upp en sträng baserat på en avgränsare baserat på regex och returnerar en matris med strängar * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Ersätt alla förekomster av en under sträng med en annan under sträng i den aktuella strängen. Om den sista parametern utelämnas, är standardvärdet tom sträng * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Kastar om en sträng * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
Extraherar en under sträng med antalet tecken till höger. Samma som del sträng (Str, LENGTH (Str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kontrollerar om strängen matchar angivet regex-mönster * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Avrundar ett tal till en valfri skala och ett valfritt avrundnings läge. Om skalan utelämnas används värdet 0 som standard.  Om läget utelämnas, är det standard ROUND_HALF_UP (5). Värdena för avrundning inkluderar 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Tilldelar sekventiella rad nummer för rader i ett fönster som börjar med 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
Höger pad strängen med den angivna utfyllnaden tills den har en viss längd. Om strängen är lika med eller större än längden, trimmas den till längden * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
Höger trimmar en sträng med inledande tecken. Om den andra parametern inte anges rensas blank steg. Annars trimmas alla bokstäver som anges i den andra parametern * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar det andra värdet för ett datum. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Den lokala tids zonen används som standard. Se Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Varaktighet i millisekunder för antal sekunder * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar SHA-1-sammandrag av uppsättning av varierande primitiva data typer och returnerar en hex-sträng på 40 tecken. Det kan användas för att beräkna ett finger avtryck för en rad * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar SHA-2-sammandrag av en uppsättning av varierande primitiva data typer med en bit-längd som bara kan vara av värdena 0 (256), 224, 256, 384, 512. Det kan användas för att beräkna ett finger avtryck för en rad * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar ett sinus-värde * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar ett hyperboliskt sinus-värde * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar snedheten för en kolumn * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar du snedheten för en kolumn * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Extraherar en delmängd av en matris från en position. Positionen är 1 baserad. Om längden utelämnas används standardvärdet i slutet av strängen * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Sorterar matrisen med den angivna predikatet-funktionen. Sortering förväntar sig en referens till två på varandra följande element i uttrycks funktionen som #item1 och #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Hämtar SOUNDEX-koden för strängen * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Delar upp en sträng baserat på en avgränsare och returnerar en matris med strängar * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Beräknar kvadratroten ur ett tal * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Kontrollerar om strängen börjar med den angivna strängen * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar standard avvikelsen för en kolumn * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar standard avvikelsen för en kolumn * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar populationens standard avvikelse för en kolumn * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar populationens standard avvikelse för en kolumn * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar exempel standard avvikelsen för en kolumn * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar exempel standard avvikelsen för en kolumn * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Subtrahera månader från ett datum eller en tidsstämpel. Samma som operatorn för date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Subtrahera månader från ett datum eller tidsstämpel * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Extraherar en under sträng av en viss längd från en position. Positionen är 1 baserad. Om längden utelämnas används standardvärdet i slutet av strängen * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Hämtar den sammanlagda summan av en numerisk kolumn * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar den sammanlagda summan av distinkta värden för en numerisk kolumn * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på kriterier får den sammanlagda summan av en numerisk kolumn. Villkoret kan baseras på valfri kolumn * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Baserat på kriterier får den sammanlagda summan av en numerisk kolumn. Villkoret kan baseras på valfri kolumn * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Beräknar ett tangens-värde * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Beräknar ett hyperboliskt tangens-värde * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Konverterar alla tal/datum/tidsstämpel/sträng till binär representation * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
Konverterar värdet (t, true, y, Yes, 1) till true och (' f ', ' false ', ' no ', ' 0 ') till false och NULL för andra värden * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konverterar datum strängen för indata till datum med ett valfritt datum format för indata. Se Java-SimpleDateFormat för tillgängliga format. Om datum formatet för indata utelämnas, är standardformatet åååå-[M] M-[d] d. Godkända format: [åååå, åååå-[M] M, åååå-[M] M-[d] d, åååå-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Konverterar valfri numerisk eller sträng till ett Decimal värde. Om ingen precision och skalning har angetts är den standard till (10, 2). Ett valfritt Java decimal format kan användas för konverteringen. Ett valfritt språk format i form av BCP47-språk som en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Konverterar alla numeriska eller strängar till ett Double-värde. Ett valfritt Java decimal format kan användas för konverteringen. Ett valfritt språk format i form av BCP47-språk som en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Konverterar alla numeriska eller strängar till ett flytt ALS värde. Ett valfritt Java decimal format kan användas för konverteringen. Trunkerar alla dubbla * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konverterar valfri numerisk eller sträng till ett heltals värde. Ett valfritt Java decimal format kan användas för konverteringen. Trunkerar alla långa, flytande, dubbla * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konverterar valfri numerisk eller sträng till ett långt värde. Ett valfritt Java decimal format kan användas för konverteringen. Trunkerar alla flyttal, dubbla * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konverterar alla numeriska eller strängar till ett kort värde. Ett valfritt Java decimal format kan användas för konverteringen. Trunkerar alla heltal, långa, flyttal, dubbla * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konverterar en primitiv datatyp till en sträng. För tal och datum kan ett format anges. Om det är ospecificerat har system standard valts. Java decimal format används för tal. Se Java-SimpleDateFormat för alla möjliga datum format; Standardformatet är åååå-MM-DD * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Konverterar en sträng till en tidstämpel som har fått ett valfritt tidsstämpel-format. Se Java-SimpleDateFormat för alla möjliga format. Om tidsstämpeln utelämnas används standard mönstret. åååå-[M] M-[d] d hh: mm: SS [. f...]. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Tidsstämpeln har stöd för upp till millisekunder med värdet 999Refer Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Konverterar tidsstämpeln till UTC. Du kan skicka en valfri tidszon i formatet "GMT", "PST", "UTC", "America/Cayman". Standardvärdet är den aktuella timezoneRefer Java-SimpleDateFormat för tillgängliga format. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Ersätt en uppsättning tecken med en annan uppsättning tecken i strängen. Tecken har 1 till 1 ersättning * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Trimmar en sträng med inledande och avslutande tecken. Om den andra parametern inte anges rensas blank steg. Annars trimmas alla bokstäver som anges i den andra parametern * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Returnerar alltid ett sant värde. Använd Function-syntaxen (true ()) om det finns en kolumn med namnet "true" * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Matchar kolumnens typ. Kan endast användas i mönster uttryck. antalet matchar Short, Integer, Long, Double, float eller decimal, integral matchar kort, heltal, lång, bråk matchad, Float, decimal och datetime matchnings datum eller timestamp-typ * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Versaler en sträng * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Returnerar den genererade UUID-* ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar var Ian sen för en kolumn * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar var Ian sen för en kolumn * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar populationens varians för en kolumn * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar populationens varians för en kolumn * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar den kompensations bara var Ian sen för en kolumn * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Baserat på ett villkor hämtar den ej förvägd variansen för en kolumn * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Hämtar veckan på året angivet datum * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Varaktighet i millisekunder för antal veckor * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Operator för logisk XOR. Samma som ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Hämtar årets värde för ett datum * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder uttrycks verktyget](concepts-data-flow-expression-builder.md).
