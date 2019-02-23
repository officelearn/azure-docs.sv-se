---
title: Processen kan konfigureras tröskelvärdesbaserade regler i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder referensdata för att uppnå en datastyrd aviseringslösning med konfigurerbara tröskelvärdesbaserade regler i Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731199"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Bearbeta konfigurerbara tröskelbaserade regler i Azure Stream Analytics
Den här artikeln beskriver hur du använder referensdata för att uppnå en datastyrd aviseringslösning som använder konfigurerbara tröskelbaserade regler i Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: Aviseringar baserat på tröskelvärden för justerbara regler
Du kan behöva skapa en avisering som utdata när inkommande strömmas händelser har uppnått ett visst värde, eller när ett aggregerat värde baserat på inkommande strömmas händelser överskrider ett visst tröskelvärde. Det enkelt att konfigurera ett Stream Analytics-frågan som jämfört med värdet till ett statiskt tröskelvärde som är fast och förinställda. Ett fast tröskelvärde kan vara hårdkodade i strömmande frågesyntaxen med hjälp av enkla numeriska jämförelser (större än, mindre än- och likhetsfrågor).

I vissa fall kan behöva tröskelvärdena konfigureras enkelt utan att redigera frågesyntaxen varje gång som ett tröskelvärde ändras. I andra fall kan behöva du ett stort antal enheter eller användare som behandlats av samma fråga med var och en av dem med en annan tröskelvärdena på varje typ av enhet. 

Det här mönstret kan användas för att dynamiskt konfigurera tröskelvärden för selektivt välja vilken typ av enhet tröskelvärdet gäller genom att filtrera indata och selektivt välja vilka fält som ska ingå i utdata.

## <a name="recommended-design-pattern"></a>Rekommenderade designmönster
Använd en referensindata till ett Stream Analytics-jobb som en sökning av tröskelvärdena:
- Store tröskelvärdena i referensdata, ett värde per nyckel.
- Anslut de strömmande data inmatningshändelser till referensdata på nyckelkolumn.
- Använd knappade värdet från referensdata som tröskelvärdet.

## <a name="example-data-and-query"></a>Exempeldata och fråga
I det här exemplet genereras aviseringar när aggregering av data som strömmas i från enheter i ett fönster för minuter långa matchar värdena som anges i regeln som angavs som referensdata.

I frågan, för varje deviceId och varje metricName under deviceId, kan du konfigurera från 0 till 5 dimensioner till GROUP BY. Endast de händelser som har motsvarande filtervärdena grupperas. När grupperade, beräknas tidsanpassade samlingar av Min, Max, Avg, över en 60-sekunders rullande fönster. Filter på de sammanställda värdena beräknas sedan enligt det konfigurerade tröskelvärdet i reference att generera avisering utdatahändelse.

Anta till exempel det är ett Stream Analytics-jobb som har en referensindata med namnet **regler**, och strömning av indata som heter **mått**. 

## <a name="reference-data"></a>Referensdata
Referensdata för det här exemplet visar hur en tröskelbaserade regel kan framställas. En JSON-fil innehåller referensdata och sparas i Azure blob-lagring och den blob storage-behållaren används som en referensindata med namnet **regler**. Du kan skriva över den här JSON-filen och Ersätt regelkonfigurationen med tiden, utan att stoppa eller starta direktuppspelningsjobbet.

- Exempelregeln används för att representera en justerbar avisering när CPU överskrider (genomsnitt är större än eller lika med) värdet `90` procent. Den `value` fältet kan konfigureras vid behov.
- Observera att regeln har ett **operatorn** fält som tolkas dynamiskt senare i frågesyntaxen `AVGGREATEROREQUAL`. 
- Regeln filtrerar data på en viss dimension nyckel `2` med värdet `C1`. Andra fält är tom sträng, som anger inte om du vill filtrera Indataströmmen av dessa fält. Du kan ställa in ytterligare CPU-regler att filtrera andra matchande fält efter behov.
- Alla kolumner är som ska ingå i utdata varning avisering. I det här fallet `includedDim` nyckeln nummer `2` aktiveras `TRUE` att representera att fältnumret 2 av händelsedata i strömmen ska tas med i bestämda utdata-händelser. I andra fält ingår inte i aviseringen utdata, men fältlistan kan justeras.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Exempelfråga för direktuppspelning
Det här exemplet Stream Analytics-frågan kopplar ihop den **regler** referensdata från exemplet ovan, till en inkommande dataström för med namnet **mått**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Exempel inmatningshändelse strömningsdata
Det här exemplet JSON-data som representerar den **mått** indata som används i frågan ovan direktuppspelning. 

- Tre exempel på händelser som är listade inom 1 minut-tiden, värdet `T14:50`. 
- Alla tre har samma `deviceId` värdet `978648`.
- CPU-måttvärden varierar i varje händelse `98`, `95`, `80` respektive. Endast de första två exempel på händelserna överskrida CPU varningsregeln i regeln.
- Fältet includeDim i varningsregeln var viktiga nummer 2. Motsvarande nyckel 2 fält i händelserna exempel heter `NodeName`. De tre exempel på händelserna har värden `N024`, `N024`, och `N014` respektive. I utdata visas endast i noden `N024` som är de enda data som matchar aviseringsvillkoren för hög processor. `N014` uppfyller inte hög CPU-tröskelvärdet.
- Varningsregeln har konfigurerats med en `filter` endast för viktiga nummer 2, vilket motsvarar den `cluster` i exempelhändelser. Alla tre exempel händelserna har värdet `C1` och matchar ett angivet kriterium.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Exempel på utdata
Det här exemplet utdata JSON data visas som en enda avisering händelse tillhandahålls baserat på CPU-tröskelvärdet-regel som definierats i referensdata. Händelsen utdata innehåller namnet på aviseringen, samt den sammanställda (genomsnitt, min, max) fältens anses vara. Händelsedata utdata innehåller viktiga fältnumret 2 `NodeName` värdet `N024` på grund av regelkonfigurationen. (JSON har ändrats för att visa radbrytningar för läsbarhet.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```