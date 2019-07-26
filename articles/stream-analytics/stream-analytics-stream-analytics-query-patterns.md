---
title: Vanliga fråge mönster i Azure Stream Analytics
description: Den här artikeln beskriver ett antal vanliga fråge mönster och design som är användbara i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 729385a2ce9feb6e69f9be29c2175b403093be3f
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413365"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Fråge exempel för vanliga Stream Analytics användnings mönster

Frågor i Azure Stream Analytics uttrycks i ett SQL-liknande frågespråk. Språk konstruktionerna dokumenteras i referens hand boken för [Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference) . 

Frågans design kan uttrycka enkel direkt sändnings logik för att flytta händelse data från en indataströmmen till ett utgående data lager, eller så kan den utföra omfattande mönster matchning och temporal analys för att beräkna mängder över olika tidsfönster som i [skapa en IoT-lösning med använda Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) guide. Du kan koppla data från flera indata till att kombinera strömmande händelser och du kan göra sökningar mot statiska referens data för att utöka händelse värden. Du kan också skriva data till flera utdata.

Den här artikeln beskriver lösningar på flera vanliga fråge mönster baserade på verkliga scenarier.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Arbeta med komplexa datatyper i JSON och AVRO

Azure Stream Analytics stöder bearbetning av händelser i CSV-, JSON-och Avro data format.

Både JSON och Avro kan innehålla komplexa typer som kapslade objekt (poster) eller matriser. Mer information om hur du arbetar med dessa komplexa data typer finns i artikeln [parsa JSON och Avro data](stream-analytics-parsing-json.md) .

## <a name="query-example-convert-data-types"></a>Exempel på frågor: Konvertera data typer

**Beskrivning**: Definiera typer av egenskaper i indataströmmen. Exempelvis kommer bilens vikt att skickas till indataströmmen som strängar och måste konverteras till **heltal** för att utföra **Sum**.

Inmatade:

| Skapa | Time | Vikt |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Utdata**:

| Skapa | Vikt |
| --- | --- |
| Honda |3000 |

**Lösning**:

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

**Förklaring**: Använd en **Cast** -instruktion i fältet **vikt** för att ange dess datatyp. Se listan över data typer som stöds i [data typer (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exempel på frågor: Använd LIKE/inte gilla-mönster matchning

**Beskrivning**: Kontrol lera att ett fält värde i händelsen matchar ett visst mönster.
Kontrol lera till exempel att resultatet returnerar licens plattor som börjar med A och slutar med 9.

Inmatade:

| Skapa | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| Skapa | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Lösning**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Förklaring**: Använd **like** -instruktionen för att kontrol lera värdet för fältet **LicensePlate** . Den ska börja med bokstaven A, sedan ha en sträng på noll eller flera tecken och sedan sluta med siffran 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exempel på frågor: Ange logik för olika fall/värden (CASE-instruktioner)

**Beskrivning**: Ange en annan beräkning för ett fält baserat på ett visst kriterium. Du kan till exempel ange en sträng beskrivning för hur många bilar av samma som skickas, med ett specialfall för 1.

Inmatade:

| Skapa | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| CarsPassed | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Lösning**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Förklaring**: **Case** -uttrycket jämför ett uttryck med en uppsättning enkla uttryck för att fastställa resultatet. I det här exemplet gör fordonet med ett antal 1 returnerat en annan sträng beskrivning än vehikeln med ett annat antal än 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Exempel på frågor: Skicka data till flera utdata

**Beskrivning**: Skicka data till flera utgående mål från ett enda jobb. Analysera till exempel data för en tröskel-baserad avisering och arkivera alla händelser till Blob Storage.

Inmatade:

| Skapa | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Skapa | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Skapa | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Lösning**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
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

**Förklaring**: **Into** -satsen visar Stream Analytics vilka utdata som data ska skrivas till från den här instruktionen. Den första frågan är en direkt uppspelning av de data som tagits emot till utdata med namnet **ArchiveOutput**. Den andra frågan gör viss enkel agg regering och filtrering, och skickar resultatet till ett underordnat varnings system, **AlertOutput**.

Observera att du också kan återanvända resultatet av de vanliga tabell uttrycken (CTE) (till exempel **with** -instruktioner) i flera output-uttryck. Det här alternativet har extra fördelen att öppna färre läsare till Indatakällan.

Exempel: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Exempel på frågor: Räkna unika värden

**Beskrivning**: Räkna antalet unika fält värden som visas i data strömmen i ett tids fönster. Till exempel hur många unika bilar som skickas via avgiftsbelagt monter i ett 2-sekunders fönster?

Inmatade:

| Skapa | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Utdata:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Lösa**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Förklaring:** 
**Count (DISTINCT fabrikat)** returnerar antalet distinkta värden i kolumnen **skapa** i ett tids fönster.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exempel på frågor: Avgöra om ett värde har ändrats

**Beskrivning**: Titta på ett tidigare värde för att avgöra om det skiljer sig från det aktuella värdet. Är till exempel föregående bil på väg vägen samma sak som den nuvarande bilen?

Inmatade:

| Skapa | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Utdata**:

| Skapa | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Lösning**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Förklaring**: Använd **fördröjning** för att titta på indata strömmar en händelse tillbaka och få **fram värdet.** Jämför det sedan med värdet **gör** i den aktuella händelsen och mata ut händelsen om de skiljer sig åt.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exempel på frågor: Hitta den första händelsen i ett fönster

**Beskrivning**: Hitta den första bilen under varje 10-minuters intervall.

Inmatade:

| LicensePlate | Skapa | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Skapa | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Lösning**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Nu ska vi ändra problemet och hitta den första bilen i ett visst intervall under 10 minuter.

| LicensePlate | Skapa | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösning**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Exempel på frågor: Hitta den sista händelsen i ett fönster

**Beskrivning**: Hitta den sista bilen under varje 10-minuters intervall.

Inmatade:

| LicensePlate | Skapa | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Skapa | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösning**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Förklaring**: Det finns två steg i frågan. Den första, hittar den senaste tidsstämpeln i 10 minuters fönster. I det andra steget kopplas resultatet från den första frågan med den ursprungliga data strömmen för att hitta de händelser som matchar de senaste tidsstämplar i varje fönster. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Exempel på frågor: Hitta korrelerade händelser i en ström

**Beskrivning**: Hitta korrelerade händelser i en data ström. Du kan till exempel ha två på varandra följande bilar från samma fabrikat som du angav på väg inom de senaste 90 sekunderna?

Inmatade:

| Skapa | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Utdata**:

| Skapa | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Lösning**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Förklaring**: Använd **fördröjning** för att titta på indata strömmar en händelse tillbaka och få **fram värdet.** Jämför det med värdet **gör** i den aktuella händelsen och mata sedan ut händelsen om de är samma. Du kan också använda en **fördröjning** för att hämta data om föregående bil.

## <a name="query-example-detect-the-duration-between-events"></a>Exempel på frågor: Identifiera varaktigheten mellan händelser

**Beskrivning**: Hitta varaktigheten för en specifik händelse. Om du till exempel har en webb-klick Ströms bestämmer du hur lång tid en funktion får ta.

Inmatade:  

| Användare | Funktion | Händelse | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |slutpunkt |2015-01-01T00:00:08.0000000Z |

**Utdata**:  

| Användare | Funktion | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Lösning**:

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

**Förklaring**: Använd den **sista** funktionen för att hämta det senaste **tids** värdet när händelse typen **startades**. Den **sista** funktionen använder **partition av [användare]** för att indikera att resultatet beräknas per unik användare. Frågan har ett maximalt 1 timmes tröskelvärde för tids skillnaden mellan **Start** -och **stopp** händelser, men kan konfigureras efter behov **(gräns längd (timme, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exempel på frågor: Identifiera varaktigheten för ett villkor
**Beskrivning**: Ta reda på hur länge ett villkor inträffat.
Anta till exempel att en bugg ledde till att alla bilar har en felaktig vikt (över 20 000 kg) och varaktigheten för denna bugg måste beräknas.

Inmatade:

| Skapa | Time | Vikt |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Utdata**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Lösning**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Förklaring**: Använd **fördröjning** för att Visa indataströmmen i 24 timmar och leta efter instanser där **StartFault** och **StopFault** sträcker sig över vikt < 20000.

## <a name="query-example-fill-missing-values"></a>Exempel på frågor: Fyll värden som saknas

**Beskrivning**: För en data ström med händelser som saknar värden skapar du en ström med händelser med regelbundna intervall. Du kan till exempel generera en händelse var femte sekund som rapporterar den senast visade data punkten.

Inmatade:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Utdata (första 10 raderna)** :

| windowend | lastevent. t | lastevent. Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10 000 Z |2014-01-01T14:01:10 000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Lösning**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Förklaring**: Den här frågan genererar händelser var femte sekund och matar ut den senaste händelsen som togs emot tidigare. [Hoppande](/stream-analytics-query/hopping-window-azure-stream-analytics) -fönstrets varaktighet avgör hur långt tillbaka frågan söker efter den senaste händelsen (300 sekunder i det här exemplet).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exempel på frågor: Korrelera två händelse typer inom samma data ström

**Beskrivning**: Ibland måste aviseringar genereras baserat på flera händelse typer som har inträffat under ett visst tidsintervall. I ett IoT-scenario för hem ugnar måste en avisering till exempel genereras när fläkt temperaturen är mindre än 40 och den högsta kraften under de senaste 3 minuterna är mindre än 10.

Inmatade:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | styr |120 |
| "2018-01-01T16:01:00" | "Oven1" | befogenhet |15 |
| "2018-01-01T16:02:00" | "Oven1" | styr |100 |
| "2018-01-01T16:02:00" | "Oven1" | befogenhet |15 |
| "2018-01-01T16:03:00" | "Oven1" | styr |70 |
| "2018-01-01T16:03:00" | "Oven1" | befogenhet |15 |
| "2018-01-01T16:04:00" | "Oven1" | styr |50 |
| "2018-01-01T16:04:00" | "Oven1" | befogenhet |15 |
| "2018-01-01T16:05:00" | "Oven1" | styr |30 |
| "2018-01-01T16:05:00" | "Oven1" | befogenhet |8 |
| "2018-01-01T16:06:00" | "Oven1" | styr |20 |
| "2018-01-01T16:06:00" | "Oven1" | befogenhet |8 |
| "2018-01-01T16:07:00" | "Oven1" | styr |20 |
| "2018-01-01T16:07:00" | "Oven1" | befogenhet |8 |
| "2018-01-01T16:08:00" | "Oven1" | styr |20 |
| "2018-01-01T16:08:00" | "Oven1" | befogenhet |8 |

**Utdata**:

| eventTime | deviceId | styr | Alertmessage som | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "" Korts matnings element " |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "" Korts matnings element " |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "" Korts matnings element " |15 |

**Lösning**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Förklaring**: Den första frågan `max_power_during_last_3_mins`använder [glidande fönster](/stream-analytics-query/sliding-window-azure-stream-analytics) för att hitta det högsta värdet för ström sensorn för varje enhet under de senaste 3 minuterna. Den andra frågan är kopplad till den första frågan för att hitta det energi värde i det senaste fönstret som är relevant för den aktuella händelsen. Under förutsättning att villkoren uppfylls genereras en avisering för enheten.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Exempel på frågor: Bearbeta händelser oberoende av enhetens klock skevning (under strömmar)

**Beskrivning**: Händelser kan komma in i försenat eller ur ordning på grund av fördröjningar mellan evenemangs producenter, klockor mellan partitioner eller nätverks fördröjning. I följande exempel är enhets klockan för TollID 2 fem sekunder bakom TollID 1 och enhets klockan för TollID 3 är tio sekunder bakom TollID 1. 

Inmatade:

| LicensePlate | Skapa | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Utdata**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Lösning**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Förklaring**: Timestamp by-satsen söker på varje enhets tids linje separat med hjälp av [under](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) strömmar. Utmatnings händelserna för varje TollID genereras när de beräknas, vilket innebär att händelserna är i ordning för varje TollID i stället för att omordnas som om alla enheter var på samma klocka.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Exempel på frågor: Ta bort duplicerade händelser i ett fönster

**Beskrivning**: När du utför en åtgärd, till exempel beräkning av genomsnitt över händelser inom ett angivet tidsintervall, ska duplicerade händelser filtreras. I följande exempel är den andra händelsen en dubblett av den första.

Inmatade:  

| DeviceId | Time | Attribut | Value |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatur |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatur |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Temperatur |100 |

**Utdata**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Lösning**:

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

**Förklaring**: [Count (distinkt tid)](/stream-analytics-query/count-azure-stream-analytics) returnerar antalet distinkta värden i kolumnen Time i ett tids fönster. Du kan sedan använda utdata från det här steget för att beräkna genomsnitt per enhet genom att ta bort dubbletter.

## <a name="geofencing-and-geospatial-queries"></a>Polystaket och geospatiala frågor
Azure Stream Analytics innehåller inbyggda geospatiala funktioner som kan användas för att implementera scenarier som hantering av flottan, delning av delning, anslutna bilar och till gångs spårning. Geospatiala data kan matas in i antingen interjson-eller well-format som en del av händelse data strömmen eller referens data. Mer information finns i avsnittet om avgränsnings- [och geospatiala agg regerings scenarier med Azure Stream Analytics](geospatial-scenarios.md) artikel.

## <a name="language-extensibility-through-javascript-and-c"></a>Språk utökning via Java Script ochC#
Azure Stream ananlytics Query langugae kan utökas med anpassade funktioner skrivna i Java Script C# eller språk. Mer information finns i foolowing-artiklarna:
* [Azure Stream Analytics JavaScript-användardefinierade funktioner](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics användardefinierade JavaScript-mängder](stream-analytics-javascript-user-defined-aggregates.md)
* [Utveckla .NET standard-användardefinierade funktioner för Azure Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

