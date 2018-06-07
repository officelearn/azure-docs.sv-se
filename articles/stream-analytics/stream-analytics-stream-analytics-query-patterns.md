---
title: Vanliga frågemönster i Azure Stream Analytics
description: Den här artikeln beskriver ett antal vanliga frågemönster och modeller som är användbara i Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: f63ccd62136fe8d556a4cfb591e3294f3751dfb3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652254"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exempel på vanliga Stream Analytics användningsmönster fråga

## <a name="introduction"></a>Introduktion
Frågorna i Azure Stream Analytics uttrycks i ett SQL-liknande frågespråk. Språkkonstruktioner dokumenteras i den [Stream Analytics fråga Språkreferens](https://msdn.microsoft.com/library/azure/dn834998.aspx) guide. 

Frågedesigntillståndet kan snabba och enkla direkt logik för att flytta händelsedata från en indataström till en annan utdata-datalagret. Det kan göra eller omfattande mönster matchande och temporal analys för att beräkna mängder över olika tidsfönster som i exemplet TollApp. Du kan ansluta till data från flera inmatningar för kombinera strömning händelser och gör sökningar mot statiska referensdata att utöka händelsevärden. Du kan också skriva data till flera utdata.

Den här artikeln beskrivs lösningar på flera vanliga frågemönster, baserat på verkliga scenarier. Det är ett pågående arbete och fortsätter att uppdateras med nya mönster kontinuerligt.

## <a name="query-example-convert-data-types"></a>Frågan exempel: konvertera-datatyper
**Beskrivning**: definiera vilka typer av egenskaper på Indataströmmen.
Till exempel bil vikten kommer på Indataströmmen som strängar och behöver konverteras till **INT** att utföra **SUMMAN** den.

**Indata**:

| Kontrollera | Tid | Vikt |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Utdata**:

| Kontrollera | Vikt |
| --- | --- |
| Honda |3000 |

**Lösningen**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Förklaring**: Använd en **OMVANDLINGEN** instruktion i den **vikt** fältet för att ange den aktuella datatypen. Visa en lista över vilka datatyper i [datatyper (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Frågan exempel: liknande/inte vill mönstret matchar användning
**Beskrivning**: kontrollerar att ett fältvärde vid händelsen som matchar ett visst mönster.
Till exempel kontrollera att resultatet returnerar licens nivåer som börjar med ett och slutar med 9.

**Indata**:

| Kontrollera | LicensePlate | Tid |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| Kontrollera | LicensePlate | Tid |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Lösningen**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Förklaring**: Använd den **som** -instruktionen för att kontrollera den **LicensePlate** fältet värde. Det måste börja med en A-, och sedan har ett antal noll eller fler tecken och sedan avslutas med en 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Frågan exempel: Ange logik för olika fall/värden (CASE-satser)
**Beskrivning**: Ange en annan beräkning för ett fält baserat på ett visst kriterium.
Ange till exempel en sträng beskrivning för att se hur många bilar av samma skickas med ett specialfall för 1.

**Indata**:

| Kontrollera | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Utdata**:

| CarsPassed | Tid |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Lösningen**:

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

**Förklaring**: den **FALLET** uttryck jämför ett uttryck som en uppsättning enkla uttryck för att fastställa resultatet. I det här exemplet gör vehicle med ett antal 1 returnerade en annan sträng beskrivning än vehicle gör med ett antal än 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Frågan exempel: skicka data till flera utdata
**Beskrivning**: skicka data till flera mål i utdata från ett enda utskriftsjobb.
Till exempel analysera data för en avisering om tröskelvärdesbaserad och arkivera alla händelser till blob storage.

**Indata**:

| Kontrollera | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Kontrollera | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Kontrollera | Tid | Antal |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Lösningen**:

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

**Förklaring**: den **INTO** satsen instruerar Stream Analytics som utdata att skriva data till från den här satsen.
Den första frågan är en direktlagringsdisk av alla data att utdata som heter **ArchiveOutput**.
Den andra frågan har några enkla aggregering och filtrering och skickar resultatet till en underordnad aviseringar system.

Observera att du kan också återanvända resultaten av cte (cte-referenser) (som **WITH** instruktioner) i flera instruktioner i utdata. Det här alternativet har den ytterligare fördelen med att öppna färre läsare till Indatakällan.
Exempel: 

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

## <a name="query-example-count-unique-values"></a>Frågan exempel: Räkna antalet unika värden
**Beskrivning**: Räkna antalet unika värdena som visas i dataströmmen inom ett tidsintervall.
Till exempel hur många unika gör bilar passerat avgift monter i ett fönster med 2 sekunder?

**Indata**:

| Kontrollera | Tid |
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

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Förklaring:**
**COUNT (DISTINKTA gör)** returnerar antalet distinkta värden i den **Se** kolumnen inom ett tidsintervall.

## <a name="query-example-determine-if-a-value-has-changed"></a>Frågan exempel: fastställa om ett värde har ändrats
**Beskrivning**: Titta på en tidigare värde för att bestämma om det skiljer sig från det aktuella värdet.
Tidigare bilen på väg avgift är till exempel göra samma som aktuella bilen?

**Indata**:

| Kontrollera | Tid |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Utdata**:

| Kontrollera | Tid |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Lösningen**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Förklaring**: Använd **FÖRDRÖJNING** för att granska i Indataströmmen en händelsen tillbaka och få de **Se** värde. Jämför dem med den **Se** -värdet för den aktuella händelsen och utdata händelsen om de är olika.

## <a name="query-example-find-the-first-event-in-a-window"></a>Frågan exempel: Sök efter den första händelsen i ett fönster
**Beskrivning**: hitta första bil i varje 10 minuters intervall.

**Indata**:

| LicensePlate | Kontrollera | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Kontrollera | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Lösningen**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Nu ska vi ändra problemet och söka efter en viss kontrollera första bilen i var 10 minuters intervall.

| LicensePlate | Kontrollera | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösningen**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Frågan exempel: Sök efter den sista händelsen i ett fönster
**Beskrivning**: hitta sista vagnen i varje 10 minuters intervall.

**Indata**:

| LicensePlate | Kontrollera | Tid |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Utdata**:

| LicensePlate | Kontrollera | Tid |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Lösningen**:

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

**Förklaring**: det finns två steg i frågan. Den första som söker efter senaste tidsstämpeln i windows 10 minuter. Det andra steget kopplas resultatet av den första frågan med ursprungliga direkt för att söka efter de händelser som matchar de senaste tidsstämplarna i varje fönster. 

## <a name="query-example-detect-the-absence-of-events"></a>Frågan exempel: identifiera frånvaro av händelser
**Beskrivning**: Kontrollera att en ström som inte har något värde som matchar vissa villkor.
Till exempel 2 på varandra följande bilar från samma Se angett avgift väg inom de senaste 90 sekunderna?

**Indata**:

| Kontrollera | LicensePlate | Tid |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF 987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Utdata**:

| Kontrollera | Tid | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Lösningen**:

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

**Förklaring**: Använd **FÖRDRÖJNING** för att granska i Indataströmmen en händelsen tillbaka och få de **Se** värde. Jämför dem med den **Se** värde i den aktuella händelsen och sedan utdata händelsen om de är likadana. Du kan också använda **FÖRDRÖJNING** och hämta data om tidigare bilen.

## <a name="query-example-detect-the-duration-between-events"></a>Frågan exempel: identifiera varaktigheten mellan händelser
**Beskrivning**: hitta varaktighet för en given händelse. Till exempel ges en web clickstream bestämma tidsåtgången för en funktion.

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

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Förklaring**: Använd den **senaste** funktion för att hämta senaste **tid** värde när händelsetypen **starta**. Den **senaste** använder funktionen **PARTITION BY [användare]** att ange att resultatet beräknas per unika användare. Frågan har ett 1 timme högsta tröskelvärde för tidsskillnad mellan **starta** och **stoppa** händelser, men kan konfigureras vid behov **(GRÄNSEN DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Frågan exempel: identifiera varaktigheten för ett villkor
**Beskrivning**: ta reda på hur länge ett villkor inträffade.
Anta exempelvis att en bugg resulterade i alla bilar med en felaktig vikt (ovanför 20 000 pund) och varaktighet för det programfelet måste beräknas.

**Indata**:

| Kontrollera | Tid | Vikt |
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

````
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
````

**Förklaring**: Använd **FÖRDRÖJNING** visa Indataströmmen under 24 timmar och leta efter instanser där **StartFault** och **StopFault** omfattas av vikten < 20000.

## <a name="query-example-fill-missing-values"></a>Frågan exempel: fylla i saknade värden
**Beskrivning**: skapa en dataström med händelser med jämna mellanrum för ström av händelser som saknar värden.
Till exempel generera en händelse var femte sekund som rapporter nyligen upptäckta datapunkten.

**Indata**:

| T | värde |
| --- | --- |
| ”2014-01-01T06:01:00” |1 |
| ”2014-01-01T06:01:05” |2 |
| ”2014-01-01T06:01:10” |3 |
| ”2014-01-01T06:01:15” |4 |
| ”2014-01-01T06:01:30” |5 |
| ”2014-01-01T06:01:35” |6 |

**Utdata (första 10 raderna)**:

| windowend | lastevent.t | lastevent.Value |
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

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Förklaring**: den här frågan och genererar händelser var femte sekund och matar ut den senaste händelse som togs emot tidigare. Den [Hopping fönstret](https://msdn.microsoft.com/library/dn835041.aspx "Hopping fönstret--Azure Stream Analytics") varaktighet anger hur långt tillbaka frågan ser ut för att hitta den senaste händelsen (300 sekunder i det här exemplet).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Frågan exempel: korrelera två typer av händelser inom samma dataström
**Beskrivning**: ibland aviseringar behöver genereras baserat på flera händelsetyper som uppstått i ett visst tidsintervall.
Till exempel i en IoT-scenariot för hem ugnar måste en avisering skapas när fläkt är mindre än 40 och högsta effekt under de senaste 3 minuterna är mindre än 10.

**Indata**:

| time | deviceId | sensorName | värde |
| --- | --- | --- | --- |
| ”2018-01-01T16:01:00” | ”Oven1” | ”temp” |120 |
| ”2018-01-01T16:01:00” | ”Oven1” | ”power” |15 |
| ”2018-01-01T16:02:00” | ”Oven1” | ”temp” |100 |
| ”2018-01-01T16:02:00” | ”Oven1” | ”power” |15 |
| ”2018-01-01T16:03:00” | ”Oven1” | ”temp” |70 |
| ”2018-01-01T16:03:00” | ”Oven1” | ”power” |15 |
| ”2018-01-01T16:04:00” | ”Oven1” | ”temp” |50 |
| ”2018-01-01T16:04:00” | ”Oven1” | ”power” |15 |
| ”2018-01-01T16:05:00” | ”Oven1” | ”temp” |30 |
| ”2018-01-01T16:05:00” | ”Oven1” | ”power” |8 |
| ”2018-01-01T16:06:00” | ”Oven1” | ”temp” |20 |
| ”2018-01-01T16:06:00” | ”Oven1” | ”power” |8 |
| ”2018-01-01T16:07:00” | ”Oven1” | ”temp” |20 |
| ”2018-01-01T16:07:00” | ”Oven1” | ”power” |8 |
| ”2018-01-01T16:08:00” | ”Oven1” | ”temp” |20 |
| ”2018-01-01T16:08:00” | ”Oven1” | ”power” |8 |

**Utdata**:

| EventTime | deviceId | Temp | alertmessage som | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| ”2018-01-01T16:05:00” | ”Oven1” |30 | ”Kort krets uppvärmning element” |15 |
| ”2018-01-01T16:06:00” | ”Oven1” |20 | ”Kort krets uppvärmning element” |15 |
| ”2018-01-01T16:07:00” | ”Oven1” |20 | ”Kort krets uppvärmning element” |15 |

**Lösningen**:

````
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
````

**Förklaring**: den första frågan `max_power_during_last_3_mins`, använder den [glidande fönstret](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) att hitta maxvärdet för power sensor för varje enhet under de senaste 3 minuterna. Den andra frågan är ansluten till den första frågan för att hitta värdet för power i fönstret senaste relevanta för den aktuella händelsen. Och sedan om villkoren är uppfyllda, en varning ska genereras för enheten.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Frågan exempel: bearbeta händelser som är oberoende av enheten klockan skeva (underströmmar)
**Beskrivning**: händelser kan sen ankomst eller fel ordning på grund av att jämföra mellan händelse producenter klockan skeva mellan partitioner eller Nätverksfördröjningen. I följande exempel enhet klockan för TollID 2 är tio sekunder efter TollID 1 och enheten klockan för TollID 3 är fem sekunder efter TollID 1. 


**Indata**:
| LicensePlate | Kontrollera | Tid | TollID |
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

````
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId

````

**Förklaring**: den [TIMESTAMP BY OVER](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) satsen tittar på varje enhet tidslinjen separat med underströmmar. Utdata-händelser för varje TollID skapas de beräknade, vilket innebär att händelserna som är i ordning med avseende på varje TollID i stället för som ordnas om som om alla enheter som fanns på samma klockan.


## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

