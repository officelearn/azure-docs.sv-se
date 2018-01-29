---
title: "Fråga exempel för vanliga användningsmönster i Stream Analytics | Microsoft Docs"
description: "Vanliga frågemönster för Azure Stream Analytics"
keywords: "frågan exempel"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: 6ac5d3ab2a4df63c429f8478e392d84ac0ea6fd7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exempel på vanliga Stream Analytics användningsmönster fråga
## <a name="introduction"></a>Introduktion
Frågorna i Azure Stream Analytics uttrycks i ett SQL-liknande frågespråk. De här frågorna dokumenteras i den [Stream Analytics fråga Språkreferens](https://msdn.microsoft.com/library/azure/dn834998.aspx) guide. Den här artikeln beskrivs lösningar på flera vanliga frågemönster, baserat på verkliga scenarier. Det är ett pågående arbete och fortsätter att uppdateras med nya mönster kontinuerligt.

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

**Förklaring**: den **FALLET** satsen gör att vi kan ange en annan beräkning, baserat på vissa kriterier (i vårt fall antalet bilar i fönstret sammanställd).

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
Den första frågan är en direktlagringsdisk data togs emot för utdata som vi namnet **ArchiveOutput**.
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
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
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
Anta exempelvis att en bugg resulterade i alla bilar med en felaktig vikt (ovanför 20 000 pund). Vi vill beräkningslängd programfelet.

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

| t | värde |
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

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Förklaring**: den här frågan och genererar händelser var femte sekund och matar ut den senaste händelse som togs emot tidigare. Den [Hopping fönstret](https://msdn.microsoft.com/library/dn835041.aspx "Hopping fönstret--Azure Stream Analytics") varaktighet anger hur långt tillbaka frågan ser ut för att hitta den senaste händelsen (300 sekunder i det här exemplet).

## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

