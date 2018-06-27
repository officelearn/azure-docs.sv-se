---
title: Processen konfigurerbart tröskelvärde baserat regler i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder referensdata för att uppnå en lösning för aviseringar som har konfigurerbart tröskelvärde baserat regler i Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 802be1ad5b1029add249430ee7760002407c4641
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021633"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Bearbeta konfigurerbara tröskelvärdesbaserad regler i Azure Stream Analytics
Den här artikeln beskriver hur du använder referensdata för att uppnå ett aviseringslösning som använder konfigurerbara tröskelvärdesbaserad regler i Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: Avisering baserat på justerbara regeln tröskelvärden
Du kan behöva skapa en avisering som utdata när inkommande direktuppspelat händelser har uppnått ett visst värde, eller när ett insamlat värde baserat på inkommande direktuppspelat händelser överskrider ett visst tröskelvärde. Det enkelt att ställa in en Stream Analytics-fråga som jämfört värde med en statisk tröskelvärde som är fasta och förinställda. Ett fast tröskelvärde kan vara hårdkodad i strömmande frågesyntaxen med enkla numeriska jämförelser (större än, mindre än- och likhetsfrågor).

I vissa fall kan behöver tröskelvärdena vara enklare konfigurerbara utan att redigera frågesyntaxen varje gång ett tröskelvärde ändras. I andra fall kan behöva du flera enheter eller användare som behandlats av samma fråga till dem med en annan tröskelvärden på varje typ av enhet. 

Det här mönstret kan användas för att dynamiskt konfigurera tröskelvärden, selektivt välja vilken typ av enhet tröskelvärdet gäller genom att filtrera inkommande data och selektivt välja vilka fält som ska inkluderas i utdata.

## <a name="recommended-design-pattern"></a>Rekommenderade designmönstret
Använd en referensindata till ett Stream Analytics-jobb som en sökning efter tröskelvärdena:
- Lagra tröskelvärdena i referensdata ett värde per nyckel.
- Anslut strömmande data inkommande händelser till referensdata på nyckelkolumn.
- Använd nycklad värdet från referensdata som tröskelvärdet.

## <a name="example-data-and-query"></a>Exempeldata och fråga
I det här exemplet genereras aviseringar när mängden data som strömning i från enheter i ett fönster för minuter långa matchar värdena som anges i regeln angavs som referensdata.

I frågan, för varje deviceId och varje metricName under deviceId, kan du konfigurera från 0 till 5 dimensioner till GROUP BY. De händelser som har motsvarande filtervärdena grupperas. När grupperade, beräknad fönsteraggregeringar Min, Max, Avg, under en 60-sekunders rullande fönster. Filter för sammanställda värden beräknas sedan enligt det konfigurerade tröskelvärdet i referens för att generera avisering utdatahändelse.

Ett exempel förutsätter att det finns ett Stream Analytics-jobb som har en referensindata med namnet **regler**, och direktöverföring av indata som heter **mått**. 

## <a name="reference-data"></a>Referensdata
Referensdata för det här exemplet visar hur en tröskelvärdesbaserad regel kan representeras. En JSON-fil innehåller referensdata och sparas i Azure blob storage och som behållare för blob storage används som en referensindata med namnet **regler**. Du kan skriva över den här JSON-filen och ersätter regelkonfigurationen med tiden, utan att stoppa eller starta direktuppspelningsjobbet.

- Exempelregeln används för att representera en justerbara avisering när CPU överskrider (medelvärde är större än eller lika med) värdet `90` procent. Den `value` fältet konfigureras efter behov.
- Observera att regeln har ett **operatorn** som dynamiskt tolkas i frågesyntaxen senare på `AVGGREATEROREQUAL`. 
- Regeln filtrerar data på en viss nyckel för dimensionen `2` med värdet `C1`. Andra fält är tom sträng, som anger inte om du vill filtrera Indataströmmen av dessa fält. Du kan ställa in ytterligare CPU-regler för att filtrera andra matchande fält efter behov.
- Alla kolumner som ska inkluderas i händelsen utdata avisering. I det här fallet `includedDim` nyckeln nummer `2` är aktiverat `TRUE` att representera fältnumret 2 händelsedata i dataströmmen inkluderas i bestämda händelser som utdata. De andra fälten ingår inte i aviseringen utdata, men fältlistan kan justeras.


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

## <a name="example-streaming-query"></a>Exempelfråga för strömning
Den här exempelfråga Stream Analytics ansluter den **regler** referensdata från exemplet ovan, till en inkommande dataström med namnet **mått**.

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

## <a name="example-streaming-input-event-data"></a>Exempel strömmande inkommande händelsedata
Det här exemplet JSON-data som representerar den **mått** indata som används i frågan ovan strömning. 

- Tre exempel på händelser som är listade inom 1 minut timespan, värdet `T14:50`. 
- Alla tre har samma `deviceId` värdet `978648`.
- CPU-måttvärden varierar i varje händelse `98`, `95`, `80` respektive. Endast de första två exempel på händelserna som överskrider CPU varningsregeln upprättas i regeln.
- Fältet includeDim i varningsregeln var viktiga nummer 2. Motsvarande 2 nyckelfältet i händelser som exempel heter `NodeName`. De tre exempel på händelserna som har värden `N024`, `N024`, och `N014` respektive. I utdata, visas bara noden `N024` som är de enda data som matchar aviseringsvillkoren för hög processor. `N014` uppfyller inte det högsta CPU-tröskelvärdet.
- Varningsregeln har konfigurerats med en `filter` endast för viktiga nummer 2, som motsvarar den `cluster` i exempel-händelser. Alla tre exempel händelser har värdet `C1` och matchar filterkriterierna.

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
Det här exemplet utdata JSON data visas en varning händelse har producerats baserat på CPU tröskelvärdet regeln som definierats i referensdata. Utdata innehåller namnet på aviseringen samt de aggregerade (Genomsnittlig, min, max) i fälten anses vara. Händelsedata utdata innehåller viktiga fältnumret 2 `NodeName` värdet `N024` på grund av regelkonfigurationen. (JSON har ändrats för att visa radbrytningar för läsbarhet.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```