---
title: Vanliga frågemönster i Azure Stream Analytics
description: I den här artikeln beskrivs flera vanliga frågemönster och design som är användbara i Azure Stream Analytics-jobb.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982320"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Vanliga frågemönster i Azure Stream Analytics

Frågor i Azure Stream Analytics uttrycks på ett SQL-liknande frågespråk. Språkkonstruktionerna dokumenteras i [referensguiden för frågespråk i Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) 

Frågedesignen kan uttrycka enkel genomströmningslogik för att flytta händelsedata från en indataström till ett utdatalager, eller göra omfattande mönstermatchning och tidsmässig analys för att beräkna aggregat över olika tidsfönster som i [IoT-lösningen med hjälp av Stream](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Analytics-guiden. Du kan koppla data från flera indata för att kombinera strömmande händelser, och du kan göra uppslag mot statiska referensdata för att berika händelsevärdena. Du kan också skriva data till flera utdata.

I den här artikeln beskrivs lösningar på flera vanliga frågemönster baserat på verkliga scenarier.

## <a name="supported-data-formats"></a>Dataformat som stöds

Azure Stream Analytics stöder bearbetning av händelser i CSV-, JSON- och Avro-dataformat.

Både JSON och Avro kan innehålla komplexa typer som kapslade objekt (poster) eller matriser. Mer information om hur du arbetar med dessa komplexa datatyper finns i dataartikeln [Parsing JSON och AVRO.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Enkel direktfråga

En enkel genomströmningsfråga kan användas för att kopiera indata till utdata. Om till exempel en dataström som innehåller fordonsinformation i realtid måste sparas i en SQL-databas för brevanalys, gör en enkel vidarefördringsfråga jobbet.

**Ingång:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |"2000" |

**Utdata:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |"2000" |

**Fråga**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

En **SELECT** * fråga projicerar alla fält i en inkommande händelse och skickar dem till utdata. På samma sätt kan **SELECT** också användas för att endast projicera obligatoriska fält från indata. I det här exemplet, om *fordonets märke* och *tid* är de enda obligatoriska fält som ska sparas, kan dessa fält anges i **SELECT-satsen.**

**Ingång:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 (på ett sätt) |2015-01-01T00:00:04.0000000Z |1500 |

**Utdata:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:04.0000000Z |

**Fråga**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Dataaggregering över tid

Om du vill beräkna information över ett tidsfönster kan data aggregeras tillsammans. I det här exemplet beräknas ett antal under de senaste 10 minuterna för varje enskild bil märke.

**Ingång:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 (på ett sätt) |2015-01-01T00:00:04.0000000Z |1500 |

**Utdata:**

| Tillverkning | Antal |
| --- | --- |
| Make1 (på ett sätt) | 2 |
| Make2 (på ett sätt) | 1 |

**Fråga**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Denna aggregering grupperar bilarna efter *Gör* och räknar dem var 10:e sekund. Produktionen har *make and* *count* av bilar som gick igenom vägtull.

TumlandeWindow är en fönsterfunktion som används för att gruppera händelser tillsammans. En aggregering kan tillämpas över alla grupperade händelser. Mer information finns i [fönsterfunktioner](stream-analytics-window-functions.md).

Mer information om aggregering finns i [aggregerade funktioner](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Data konvertering

Data kan gjutas i **CAST** realtid med cast-metoden. Bilvikt kan till exempel konverteras från typ **nvarchar(max)** till text **bigint** och användas vid en numerisk beräkning.

**Ingång:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |"2000" |

**Utdata:**

| Tillverkning | Vikt |
| --- | --- |
| Make1 (på ett sätt) |3000 |

**Fråga**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Använd en **CAST-sats** för att ange dess datatyp. Se listan över datatyper som stöds på [datatyper (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Mer information om [datakonverteringsfunktioner](/stream-analytics-query/conversion-functions-azure-stream-analytics)finns i informationsfunktioner .

## <a name="string-matching-with-like-and-not-like"></a>Strängmatchning med LIKE och INTE SOM

**LIKE** och **INTE LIKE** kan användas för att kontrollera om ett fält matchar ett visst mönster. Ett filter kan till exempel skapas för att returnera endast registreringsskyltarna som börjar med bokstaven "A" och slutar med siffran 9.

**Ingång:**

| Tillverkning | License_plate | Tid |
| --- | --- | --- |
| Make1 (på ett sätt) |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 (på ett år) |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Utdata:**

| Tillverkning | License_plate | Tid |
| --- | --- | --- |
| Make2 (på ett sätt) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 (på ett år) |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Fråga**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Använd **LIKE-satsen** för att kontrollera **License_plate** fältvärdet. Det bör börja med bokstaven "A", sedan har någon sträng med noll eller fler tecken, som slutar med numret 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Ange logik för olika ärenden/värden (CASE-satser)

**CASE-satser** kan ge olika beräkningar för olika fält, baserat på särskilda kriterier. Till exempel, tilldela körfält "A" till bilar av *Make1* och körfält "B" till någon annan märke.

**Ingång:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:03.0000000Z |

**Utdata:**

| Tillverkning |Dispatch_to_lane | Tid |
| --- | --- | --- |
| Make1 (på ett sätt) |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |"B" |2015-01-01T00:00:02.0000000Z |

**Lösning**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE-uttrycket** jämför ett uttryck med en uppsättning enkla uttryck för att fastställa resultatet. I det här exemplet skickas fordon av *Make1* till körfält "A" medan fordon av något annat märke kommer att tilldelas körfält "B".

Mer information finns i [ärendeuttryck](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Skicka data till flera utdata

Flera **SELECT-satser** kan användas för att mata ut data till olika utdatamottagare. En **SELECT** kan till exempel mata ut en tröskelbaserad avisering medan en annan kan mata ut händelser till blob-lagring.

**Ingång:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:03.0000000Z |

**UtdataarkivUtflöde:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:03.0000000Z |

**UtdatavarningUtflöde:**

| Tillverkning | Tid | Antal |
| --- | --- | --- |
| Make2 (på ett sätt) |2015-01-01T00:00:10.000000Z |3 |

**Fråga**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO-satsen** talar om för Stream Analytics vilka av utgångarna som data ska skrivas till. Den första **SELECT** definierar en genomströmningsfråga som tar emot data från indata och skickar den till utdata med namnet **ArchiveOutput**. Den andra frågan gör några enkla aggregering och filtrering innan du skickar resultaten till en nedströms varningssystem utgång kallas **AlertOutput**.

Observera att **WITH** WITH-satsen kan användas för att definiera flera underfrågeblock. Det här alternativet har fördelen att färre läsare öppnas för indatakällan.

**Fråga**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Mer information finns i [ **WITH-satsen** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Räkna unika värden

**ANTAL** och **DISTINKT** kan användas för att räkna antalet unika fältvärden som visas i flödet inom ett tidsfönster. En fråga kan skapas för att beräkna hur många unika *bilar* som passerar genom vägtullsbåset i ett 2-sekundersfönster.

**Ingång:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:03.0000000Z |

**Resultat:**

| Count_make | Tid |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Fråga:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**ANTAL(DISTINKT MÄRKE)** returnerar antalet distinkta värden i kolumnen **Gör** inom ett tidsfönster.
Mer information finns [ **i funktionen ANTAL** mängd .](/stream-analytics-query/count-azure-stream-analytics)

## <a name="calculation-over-past-events"></a>Beräkning över tidigare händelser

**LAG-funktionen** kan användas för att titta på tidigare händelser inom ett tidsfönster och jämföra dem med den aktuella händelsen. Till exempel kan den aktuella bilen göra matas ut om det skiljer sig från den sista bilen som gick igenom vägtull.

**Ingång:**

| Tillverkning | Tid |
| --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |

**Utdata:**

| Tillverkning | Tid |
| --- | --- |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |

**Fråga**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Använd **LAG för** att kika in i indataströmmen en händelse tillbaka, hämta *make-värdet* och jämföra det med *make-värdet* för den aktuella händelsen och mata ut händelsen.

För mer information, se [**LAG.**](/stream-analytics-query/lag-azure-stream-analytics)

## <a name="retrieve-the-first-event-in-a-window"></a>Hämta den första händelsen i ett fönster

**IsFirst** kan användas för att hämta den första händelsen i ett tidsfönster. Till exempel, mata ut den första bilinformationen vid varje 10-minutersintervall.

**Ingång:**

| License_plate | Tillverkning | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 (på ett sätt) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (på ett år) |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 (på ett sätt) |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 (på ett sätt) |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 (på ett sätt) |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 (på ett sätt) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Gör4 |2015-07-27T00:13:45.0000000Z |

**Utdata:**

| License_plate | Tillverkning | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 (på ett sätt) |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 (på ett sätt) |2015-07-27T00:12:02.0000000Z |

**Fråga**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** kan också partitionera data och beräkna den första händelsen till varje specifik bil *Make* hittades vid varje 10-minuters intervall.

**Utdata:**

| License_plate | Tillverkning | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 (på ett sätt) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (på ett år) |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 (på ett sätt) |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 (på ett sätt) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Gör4 |2015-07-27T00:13:45.0000000Z |

**Fråga**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Mer information finns i [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Returnera den sista händelsen i ett fönster

När händelser förbrukas av systemet i realtid finns det ingen funktion som kan avgöra om en händelse kommer att vara den sista som anländer för det fönstret. För att uppnå detta måste indataströmmen anslutas med en annan där tiden för en händelse är den maximala tiden för alla händelser i det fönstret.

**Ingång:**

| License_plate | Tillverkning | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 (på ett sätt) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (på ett år) |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 (på ett sätt) |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 (på ett sätt) |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 (på ett sätt) |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 (på ett sätt) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Gör4 |2015-07-27T00:13:45.0000000Z |

**Utdata:**

| License_plate | Tillverkning | Tid |
| --- | --- | --- |
| VFE 1616 |Make2 (på ett sätt) |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Gör4 |2015-07-27T00:13:45.0000000Z |

**Fråga**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Det första steget i frågan hittar den maximala tidsstämpeln i 10-minutersfönster, det vill vara tidsstämpeln för den sista händelsen för det fönstret. Det andra steget sammanfogar resultatet av den första frågan med den ursprungliga strömmen för att hitta den händelse som matchar de senaste tidsstämplarna i varje fönster. 

**DATEDIFF** är en datumspecifik funktion som jämför och returnerar tidsskillnaden mellan två DateTime-fält, för mer information, referera till [datumfunktioner](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Mer information om hur du ansluter till strömmar finns [**i JOIN**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Korrelera händelser i en ström

Korrelera händelser i samma ström kan göras genom att titta på tidigare händelser med hjälp av **LAG-funktionen.** Till exempel kan en utdata genereras varje gång två på varandra följande bilar från samma *Make* gå igenom vägtull för de senaste 90 sekunderna.

**Ingång:**

| Tillverkning | License_plate | Tid |
| --- | --- | --- |
| Make1 (på ett sätt) |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 (på ett sätt) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 (på ett sätt) |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 (på ett sätt) |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Utdata:**

| Tillverkning | Tid | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Fråga**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG-funktionen** kan titta på indataströmmen en händelse tillbaka och hämta *make-värdet,* jämföra det med *make-värdet* för den aktuella händelsen.  När villkoret är uppfyllt kan data från föregående evenemang projiceras med **lag-lag i** **SELECT-uttalandet.**

För mer information, se [LAG.](/stream-analytics-query/lag-azure-stream-analytics)

## <a name="detect-the-duration-between-events"></a>Identifiera varaktigheten mellan händelser

Varaktigheten för en händelse kan beräknas genom att titta på den senaste Start-händelsen när en End-händelse tas emot. Den här frågan kan vara användbar för att bestämma vilken tid en användare spenderar på en sida eller en funktion.

**Ingång:**  

| Användare | Funktion | Händelse | Tid |
| --- | --- | --- | --- |
| user@location.com |RightMenu (högermenu) |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu (högermenu) |Slut |2015-01-01T00:00:08.0000000Z |

**Utdata:**  

| Användare | Funktion | Varaktighet |
| --- | --- | --- |
| user@location.com |RightMenu (högermenu) |7 |

**Fråga**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

Funktionen **LAST** kan användas för att hämta den sista händelsen i ett visst villkor. I det här exemplet är villkoret en händelse av typ Start, partitionering sökningen efter **PARTITION BY** användare och funktion. På så sätt behandlas varje användare och funktion oberoende av dem när du söker efter Start-händelsen. **BEGRÄNSA VARAKTIGHET** begränsar sökningen tillbaka i tiden till 1 timme mellan slut- och starthändelserna.

## <a name="detect-the-duration-of-a-condition"></a>Identifiera varaktigheten för ett villkor

För förhållanden som sträcker sig genom flera händelser kan **LAG-funktionen** användas för att identifiera varaktigheten av detta tillstånd. Anta till exempel att ett fel resulterade i att alla bilar har en felaktig vikt (över 20 000 pund) och varaktigheten av felet måste beräknas.

**Ingång:**

| Tillverkning | Tid | Vikt |
| --- | --- | --- |
| Make1 (på ett sätt) |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 (på ett sätt) |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 (på ett sätt) |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 (på ett sätt) |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 (på ett sätt) |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 (på ett sätt) |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 (på ett sätt) |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 (på ett sätt) |2015-01-01T00:00:08.0000000Z |2000 |

**Utdata:**

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Fråga**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Den första **SELECT-satsen** korrelerar den aktuella viktmätningen med föregående mätning och projicerar den tillsammans med den aktuella mätningen. Den andra **SELECT** ser tillbaka på den sista händelsen där *previous_weight* är mindre än 20000, där den nuvarande vikten är mindre än 20000 och *previous_weight* av den aktuella händelsen var större än 20000.

Den End_fault är den aktuella icke-felaktiga händelsen där den föregående händelsen var felaktig och Start_fault är den sista icke-felaktiga händelsen innan dess.

## <a name="periodically-output-values"></a>Utdatavärden med jämna mellanrum

Vid oregelbundna eller saknade händelser kan en utskrift med jämna mellanrum genereras från en mer gles datainmatning. Generera till exempel en händelse var femte sekund som rapporterar den senast sedda datapunkten.

**Ingång:**

| Tid | Värde |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Utdata (första 10 raderna):**

| Window_end | Last_event. Tid | Last_event. Värde |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Fråga**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Den här frågan genererar händelser var femte sekund och matar ut den senaste händelsen som togs emot tidigare. **Hoppingwindow-varaktigheten** avgör hur långt tillbaka frågan ser ut för att hitta den senaste händelsen.

Mer information finns i [hoppningsfönstret](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Bearbeta händelser med oberoende tid (underströmmar)

Händelser kan komma sent eller ur funktion på grund av klocksnedställning mellan händelseproducenter, klocksnedställningar mellan partitioner eller nätverksfördröjning.
Enhetsklockan för *TollID* 2 är till exempel fem sekunder efter *TollID* 1 och enhetsklockan för *TollID* 3 är tio sekunder efter *TollID* 1. En beräkning kan ske oberoende av varandra för varje vägtull, med tanke på endast sina egna klockdata som en tidsstämpel.

**Ingång:**

| LicensSkylt | Tillverkning | Tid | Avgiftsbelagdtid |
| --- | --- | --- | --- |
| DXE 5291 |Make1 (på ett sätt) |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 (på ett sätt) |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 (på ett sätt) |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 (på ett år) |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 (på ett sätt) |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 (på ett sätt) |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 (på ett år) |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Gör4 |2015-07-27T00:00:07.0000000Z | 3 |

**Utdata:**

| Avgiftsbelagdtid | Antal |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Fråga**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**TIMESTAMP OVER** BY-satsen tittar på varje enhets tidslinje oberoende av underströmmar. Utdatahändelsen för varje *TollID* genereras när de beräknas, vilket innebär att händelserna är i ordning med avseende på varje *TollID* istället för att beställas om som om alla enheter var på samma klocka.

Mer information finns i [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Ta bort dubbletthändelser i ett fönster

När du utför en åtgärd, till exempel beräkna medelvärden över händelser i ett visst tidsfönster, bör dubbletthändelser filtreras. I följande exempel är den andra händelsen en dubblett av den första.

**Ingång:**  

| DeviceId | Tid | Attribut | Värde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatur |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatur |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatur |100 |

**Utdata:**  

| Genomsnittligt värde | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Fråga**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**ANTAL(DISTINKT TID)** returnerar antalet distinkta värden i kolumnen Tid inom ett tidsfönster. Utdata från det första steget kan sedan användas för att beräkna medelvärdet per enhet genom att ignorera dubbletter.

Mer information finns i [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Session Windows

Ett sessionsfönster är ett fönster som fortsätter att expandera när händelser inträffar och stängs för beräkning om ingen händelse tas emot efter en viss tid eller om fönstret når sin maximala varaktighet.
Det här fönstret är särskilt användbart när du beräknar användarinteraktionsdata. Ett fönster startar när en användare börjar interagera med systemet och stängs när inga fler händelser observeras, vilket innebär att användaren har slutat interagera.
En användare interagerar till exempel med en webbsida där antalet klick loggas, ett sessionsfönster kan användas för att ta reda på hur länge användaren har interagerat med webbplatsen.

**Ingång:**

| User_id | Tid | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Utdata:**

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Fråga**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**SELECT projicerar** de data som är relevanta för användarinteraktionen, tillsammans med interaktionens varaktighet. Gruppera data efter användare och en **SessionWindow** som stängs om ingen interaktion sker inom 1 minut, med en maximal fönsterstorlek på 60 minuter.

Mer information om **SessionWindow**finns i [Sessionsfönstret](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Språkutökning med användardefinierad funktion i JavaScript och C #

Azure Stream Analytics frågespråk kan utökas med anpassade funktioner skrivna antingen på JavaScript- eller C#-språk. Användardefinierade funktioner (UDF) är anpassade/komplexa beräkningar som inte lätt kan uttryckas med **SQL-språket.** Dessa UDF kan definieras en gång och användas flera gånger i en fråga. En UDF kan till exempel användas för att konvertera ett hexadecimalt *nvarchar(max)* till ett *bigint-värde.*

**Ingång:**

| Device_id | HexValue ( |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Utdata:**

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Den användardefinierade funktionen beräknar *bigint-värdet* från HexValue på varje händelse som förbrukas.

Mer information finns i [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) och [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Avancerad mönstermatchning med MATCH_RECOGNIZE

**MATCH_RECOGNIZE** är en avancerad mönstermatchningsmekanism som kan användas för att matcha en sekvens av händelser med ett väldefinierat mönster för reguljära uttryck.
Till exempel övervakas en bankomat i realtid för fel, under driften av bankomaten om det finns två på varandra följande varningsmeddelanden som administratören måste meddelas.

**Ingång:**

| ATM_id | Operation_id | Return_Code | Tid |
| --- | --- | --- | --- |
| 1 | "Ange pin" | "Framgång" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Öppna pengar Slot" | "Framgång" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Closing Money Slot" | "Framgång" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Ange uttagskvantitet" | "Framgång" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Öppna pengar Slot" | "Varning" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Skriva ut banksaldo" | "Varning" | 2017-01-26T00:10:19.0000000Z |

**Utdata:**

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Öppna pengar Slot" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Den här frågan matchar minst två på varandra följande felhändelser och genererar ett alarm när villkoren är uppfyllda.
**MÖNSTER** definierar det reguljära uttryck som ska användas på matchningen, i det här fallet valfritt antal lyckade åtgärder följt av minst två på varandra följande fel.
Framgång och fel definieras med hjälp av Return_Code värde och när villkoret är uppfyllt projiceras **ÅTGÄRDERNA** med *ATM_id*, den första varningsåtgärden och den första varningstiden.

Mer information finns [i MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing och geospatiala frågor
Azure Stream Analytics tillhandahåller inbyggda geospatiala funktioner som kan användas för att implementera scenarier som vagnparkshantering, samåkning, anslutna bilar och tillgångsspårning.
Geospatiala data kan intas i geojson- eller WKT-format som en del av händelseströmmen eller referensdata.
Till exempel ett företag som är specialiserat på tillverkning av maskiner för att skriva ut pass, hyra sina maskiner till regeringar och konsulat. Placeringen av dessa maskiner är starkt kontrollerad för att undvika felplacering och eventuell användning för förfalskning av pass. Varje dator är utrustad med en GPS-tracker, den informationen vidarebefordras tillbaka till ett Azure Stream Analytics-jobb.
Tillverkningen vill hålla reda på var dessa maskiner finns och varnas om en av dem lämnar ett auktoriserat område, på så sätt kan de på distans inaktivera, varna myndigheter och hämta utrustningen.

**Ingång:**

| Equipment_id | Equipment_current_location | Tid |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Indata för referensdata:**

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47,6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Utdata:**

| Equipment_id | Equipment_alert_location | Tid |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Frågan gör det möjligt för tillverkaren att övervaka datorns plats automatiskt, få varningar när en maskin lämnar den tillåtna geofence. Den inbyggda geospatiala funktionen tillåter användare att använda GPS-data i frågan utan tredjepartsbibliotek.

Mer information finns i [geofencing- och geospatial aggregeringsscenarier med Azure Stream](geospatial-scenarios.md) Analytics-artikeln.

## <a name="get-help"></a>Få hjälp

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
