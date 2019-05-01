---
title: Vanliga frågemönster i Azure Stream Analytics
description: Den här artikeln beskriver ett antal vanliga frågemönster och -designer som är användbara i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: ef302ecaa6defc6ac0dc1dd58d4f8acc0f2fd263
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711446"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Fråga efter exempel för vanliga mönster för Stream Analytics-användning

## <a name="introduction"></a>Introduktion
Frågor i Azure Stream Analytics uttrycks i ett SQL-liknande frågespråk. Språkkonstruktioner finns dokumenterade i den [frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) guide. 

Frågans design kan snabba och enkla direkt logik för att flytta händelsedata från en Indataströmmen till ett annat datalager för utdata. Det kan också göra omfattande mönstret matchande och temporala analyser för att beräkna aggregeringar över olika tidsfönster som i exemplet TollApp. Du kan kombinera direktuppspelningshändelser genom att koppla data från olika indatakällor och göra sökningar mot statiska referensdata för att utöka händelsevärdena. Du kan också skriva data till flera utdata.

Den här artikeln beskrivs lösningar på flera vanliga frågemönster utifrån verkliga scenarier. Det är ett pågående arbete och fortsätter att uppdateras med nya mönster med jämna mellanrum.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Arbeta med komplexa datatyper i JSON och AVRO 
Azure Stream Analytics har stöd för bearbetning av händelser i CSV, JSON och Avro dataformat.
JSON- och Avro kan innehålla komplexa typer, till exempel kapslade objekt (poster) eller matriser. För att arbeta med dessa komplexa datatyper måste hänvisa till den [parsa JSON-och AVRO](stream-analytics-parsing-json.md) artikeln.


## <a name="query-example-convert-data-types"></a>Exempel på sökfråga: Konvertera-datatyper
**Beskrivning**: Ange vilka typer av egenskaper på Indataströmmen.
Till exempel bil vikten kommer på Indataströmmen som strängar och behöver konverteras till **INT** att utföra **SUMMAN** upp.

**Indata**:

| Skapa | Tid | Vikt |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Utdata**:

| Skapa | Vikt |
| --- | --- |
| Honda |3000 |

**Lösningen**:

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

**Förklaring**: Använd en **CAST** instruktionen i det **vikt** fältet för att ange dess datatyp. Se en lista över vilka datatyper i [datatyper (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exempel på sökfråga: Använd Like/Not vilja mönstret matchar
**Beskrivning**: Kontrollera att ett fältvärde på händelsen matchar ett visst mönster.
Till exempel kontrollera att resultatet returnerar licens nivåer som börjar på A och sluta med 9.

**Indata**:

| Skapa | LicensePlate | Tid |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| Skapa | LicensePlate | Tid |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Lösningen**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Förklaring**: Använd den **som** -uttrycket för att kontrollera den **LicensePlate** fältet värde. Det måste börja med en A-, och sedan har noll eller flera teckensträng och sedan avslutas med en 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exempel på sökfråga: Ange logik för olika fall/värden (CASE-utdrag)
**Beskrivning**: Ange en annan beräkning för ett fält baserat på ett visst kriterium.
Till exempel ange en sträng beskrivning för hur många bilar av samma gör skickas med ett specialfall för 1.

**Indata**:

| Skapa | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| CarsPassed | Tid |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Lösningen**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Förklaring**: Den **FALLET** uttrycket jämför ett uttryck som en uppsättning enkla uttryck för att fastställa resultatet. I det här exemplet gör vehicle och det antal 1 returneras en beskrivning av annan sträng än vehicle gör med ett antal än 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Exempel på sökfråga: Skicka data till flera utdata
**Beskrivning**: Skicka data till flera mål i utdata från ett enskilt jobb.
Till exempel analysera data för en avisering om tröskelbaserade och arkivera alla händelser till blob storage.

**Indata**:

| Skapa | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Skapa | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Skapa | Tid | Antal |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Lösningen**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
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

**Förklaring**: Den **INTO** satsen talar om Stream Analytics som utdata att skriva data till från den här instruktionen.
Den första frågan är en anslutningsfråga data som tas emot att utdata som heter **ArchiveOutput**.
Den andra frågan har en enkel aggregering och filtrering och skickar resultatet till en underordnad aviseringssystemet.

Obs Du kan även återanvända resultatet av de vanliga tabelluttryck (cte-referenser) (till exempel **WITH** uttryck) i flera instruktioner i utdata. Det här alternativet har den ytterligare fördelen med att öppna färre läsare till Indatakällan.
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

## <a name="query-example-count-unique-values"></a>Exempel på sökfråga: Antal unika värden
**Beskrivning**: Räkna antalet unika fältvärden som visas i strömmen inom ett tidsintervall.
Gör till exempel hur många unika av bilar som skickas via avgift monter i fönster 2 sekunder?

**Indata**:

| Skapa | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Utdata:**

| CountMake | Tid |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Lösning:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Förklaring:**
**antal (DISTINKTA Kontrollera)** returnerar antalet distinkta värden i den **gör** kolumnen inom ett tidsintervall.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exempel på sökfråga: Fastställa om ett värde har ändrats
**Beskrivning**: Titta på en tidigare värde för att bestämma om det skiljer sig från det aktuella värdet.
Till exempel är föregående bil på vägen avgift samma Kontrollera som den aktuella bilen?

**Indata**:

| Skapa | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Utdata**:

| Skapa | Tid |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Lösningen**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Förklaring**: Använd **FÖRDRÖJNING** att granska i Indataströmmen en händelsen tillbaka och få den **gör** värde. Jämför dem med den **gör** -värdet på den aktuella händelsen och utdata händelsen om de är olika.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exempel på sökfråga: Hitta den första händelsen i ett fönster
**Beskrivning**: Hitta den första bil i varje 10 minuters intervall.

**Indata**:

| LicensePlate | Skapa | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Skapa | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Lösningen**:

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

Nu ska vi ändra problemet och hitta den första bil för en viss kontrollerar i varje 10 minuters intervall.

| LicensePlate | Skapa | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösningen**:

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Exempel på sökfråga: Hitta den sista händelsen i ett fönster
**Beskrivning**: Hitta den senaste bilen i varje 10 minuters intervall.

**Indata**:

| LicensePlate | Skapa | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Skapa | Tid |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösningen**:

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

**Förklaring**: Det finns två steg i frågan. Den första som söker efter senaste tidsstämpeln i windows 10: e minut. Det andra steget kopplar ihop resultaten av den första frågan med ursprungliga direkt för att hitta händelser som matchar de senaste tidsstämplarna i varje fönster. 

## <a name="query-example-detect-the-absence-of-events"></a>Exempel på sökfråga: Identifiera avsaknad av händelser
**Beskrivning**: Kontrollera att en dataström har inget värde som matchar ett visst villkor.
Till exempel har 2 i följd bilar från samma Se angett avgift vägen inom de senaste 90 sekunderna?

**Indata**:

| Skapa | LicensePlate | Tid |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Utdata**:

| Skapa | Tid | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Lösningen**:

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

**Förklaring**: Använd **FÖRDRÖJNING** att granska i Indataströmmen en händelsen tillbaka och få den **gör** värde. Jämför dem med den **gör** värde i den aktuella händelsen och sedan mata ut händelsen om de är likadana. Du kan också använda **FÖRDRÖJNING** och hämta data om den tidigare bilen.

## <a name="query-example-detect-the-duration-between-events"></a>Exempel på sökfråga: Identifiera varaktigheten mellan händelser
**Beskrivning**: Hitta varaktigheten för en given händelse. Till exempel med en web-klickströmdata kan fastställa den tid som krävs för en funktion.

**Indata**:  

| Användare | Funktion | Händelse | Tid |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Slut |2015-01-01T00:00:08.0000000Z |

**Utdata**:  

| Användare | Funktion | Varaktighet |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Lösningen**:

```SQL
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Förklaring**: Använd den **senaste** funktionen för att hämta senaste **tid** värde när händelsetyp var **starta**. Den **senaste** använder **PARTITION BY [user]** att indikera att resultatet beräknas per unika användare. Frågan har 1 timme maxgränsen för tidsskillnaden mellan **starta** och **stoppa** händelser, men kan konfigureras vid behov **(GRÄNSEN DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exempel på sökfråga: Identifiera varaktigheten för ett villkor
**Beskrivning**: Ta reda på hur lång tid en tillstånd inträffade.
Anta exempelvis att en bugg resulterade i alla bilar att ha en felaktig vikt (över 20 000 pund) och varaktigheten för den buggen måste beräknas.

**Indata**:

| Skapa | Tid | Vikt |
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

**Lösningen**:

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

**Förklaring**: Använd **FÖRDRÖJNING** att visa Indataströmmen i 24 timmar och leta efter instanser var **StartFault** och **StopFault** omfattas av vikt < 20000.

## <a name="query-example-fill-missing-values"></a>Exempel på sökfråga: Fyll värden som saknas
**Beskrivning**: Generera en dataström med händelser med jämna mellanrum för dataströmmen av händelser som har värden som saknas.
Till exempel generera en händelse var femte sekund som rapporterar den nyligen upptäckta datapunkten.

**Indata**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Utdata (första 10 raderna)**:

| windowend | lastevent.t | lastevent.value |
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

**Lösningen**:

```SQL
    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Förklaring**: Den här frågan genererar händelser var femte sekund och matar ut den senaste händelsen togs emot tidigare. Den [Hopping fönstret](https://msdn.microsoft.com/library/dn835041.aspx "Hopping fönstret – Azure Stream Analytics") varaktighet som anger hur långt tillbaka frågan ser ut för att hitta den senaste händelsen (300 sekunder i det här exemplet).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exempel på sökfråga: Korrelera två händelsetyper inom samma dataström
**Beskrivning**: Aviseringar behöver ibland genereras baserat på flera händelsetyper som inträffat under ett visst tidsintervall.
Till exempel i en IoT-scenario för home ugnar en avisering måste aktiveras när temperaturen fläkt är mindre än 40 och högsta effekt under de senaste 3 minuterna är mindre än 10.

**Indata**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | ”power” |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | ”power” |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | ”power” |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | ”power” |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | ”power” |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | ”power” |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | ”power” |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | ”power” |8 |

**Utdata**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | ”Kort krets uppvärmning element” |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | ”Kort krets uppvärmning element” |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | ”Kort krets uppvärmning element” |15 |

**Lösningen**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
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

**Förklaring**: Den första frågan `max_power_during_last_3_mins`, använder den [glidande fönstret](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) att hitta maxvärdet för power sensorn för varje enhet under de senaste 3 minuterna. Den andra frågan är ansluten till den första frågan att hitta power-värdet i fönstret senaste relevanta för det aktuella. Och sedan, om villkoren är uppfyllda, en varning ska genereras för enheten.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Exempel på sökfråga: Bearbeta händelser som är oberoende av enheten klockan förskjuta (underströmmar)
**Beskrivning**: Händelser kan kommer sent eller oordnade följd av klockavvikelser mellan händelseproducenter, klockan snedställer mellan partitioner eller Nätverksfördröjningen. I följande exempel enhetens klocka för TollID 2 är fem sekunder bakom TollID 1 och enhetens klocka för TollID 3 är tio sekunder bakom TollID 1. 


**Indata**:

| LicensePlate | Skapa | Tid | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Utdata**:

| TollID | Antal |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Lösningen**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Förklaring**: Den [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) satsen tittar på varje enhet tidslinje separat enligt våra underströmmar. Utdata-händelser för varje TollID genereras som de beräknade, vilket innebär att händelserna är i ordning med avseende på varje TollID i stället för som ordnas om som om alla enheter som fanns på samma klockan.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Exempel på sökfråga: Ta bort dubbla händelser i ett fönster
**Beskrivning**: När du utför en åtgärd till exempel beräkna genomsnitt över händelser under en viss tidsperiod, ska dubbla händelserna filtreras.

**Indata**:  

| DeviceId | Tid | Attribut | Värde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatur |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatur |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatur |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatur |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatur |100 |

**Utdata**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Lösningen**:

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
        SYSTEM.TIMESTAMP
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Förklaring**: [Antal (DISTINKTA tid)](https://docs.microsoft.com/stream-analytics-query/count-azure-stream-analytics) returnerar antalet distinkta värden i kolumnen tid inom ett tidsintervall. Du kan sedan använda utdata från det här steget för att beräkna genomsnitt per enhet genom att ta bort dubbletter.

## <a name="get-help"></a>Få hjälp
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

