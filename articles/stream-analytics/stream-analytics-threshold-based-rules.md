---
title: Konfigurerbara tröskelbaserade regler i Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder referensdata för att uppnå en aviseringslösning som har konfigurerbara tröskelbaserade regler i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369719"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processkonfigurerbara tröskelvärden i Azure Stream Analytics
I den här artikeln beskrivs hur du använder referensdata för att uppnå en aviseringslösning som använder konfigurerbara tröskelbaserade regler i Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: Aviseringar baserat på justerbara regeltrösklar
Du kan behöva skapa en avisering som utdata när inkommande strömmade händelser har nått ett visst värde, eller när ett aggregerat värde baserat på inkommande strömmade händelser överskrider ett visst tröskelvärde. Det är enkelt att ställa in en Stream Analytics-fråga som jämförde värdet med ett statiskt tröskelvärde som är fast och förutbestämt. Ett fast tröskelvärde kan hårdkodas i syntaxen för direktuppspelningsfråga med enkla numeriska jämförelser (större än, mindre än och likhet).

I vissa fall måste tröskelvärdena vara lättare att konfigurera utan att redigera frågesyntaxen varje gång ett tröskelvärde ändras. I andra fall kan du behöva många enheter eller användare som bearbetas av samma fråga där var och en av dem har olika tröskelvärden på varje typ av enhet. 

Det här mönstret kan användas för att dynamiskt konfigurera tröskelvärden, selektivt välja vilken typ av enhet tröskelvärdet ska tillämpas genom att filtrera indata och selektivt välja vilka fält som ska inkluderas i utdata.

## <a name="recommended-design-pattern"></a>Rekommenderat designmönster
Använd en referensdatainmatning till ett Stream Analytics-jobb som en sökning av tröskelvärden för aviseringar:
- Lagra tröskelvärdena i referensdata, ett värde per nyckel.
- Koppla indatahändelser för strömmande data till referensdata i nyckelkolumnen.
- Använd det nyckeldana värdet från referensdata som tröskelvärde.

## <a name="example-data-and-query"></a>Exempel på data och fråga
I exemplet genereras aviseringar när summan av data som strömmas in från enheter i ett minutlångt fönster matchar de angivna värdena i regeln som tillhandahålls som referensdata.

I frågan, för varje deviceId och varje metricName under deviceId, kan du konfigurera från 0 till 5 dimensioner till GROUP BY. Endast de händelser som har motsvarande filtervärden grupperas. När grupperade, fönsterade aggregat av Min, Max, Avg, beräknas över en 60-sekunders tumlande fönster. Filter på de aggregerade värdena beräknas sedan enligt det konfigurerade tröskelvärdet i referensen för att generera aviseringsutdatahändelsen.

Anta att det finns ett Stream Analytics-jobb som har en referensdatainmatning med namnet **regler**och strömmande datainmatning med namnet **mått**. 

## <a name="reference-data"></a>Referensdata
Referensdata för det här exemplet visar hur en tröskelbaserad regel kan representeras. En JSON-fil innehåller referensdata och sparas i Azure blob storage, och den blob lagringsbehållaren används som en referensdatainmatning med namnet **regler**. Du kan skriva över den här JSON-filen och ersätta regelkonfigurationen med tiden, utan att stoppa eller starta uppspelningsjobbet.

- Exempelregeln används för att representera en justerbar avisering när processorn överskrider `90` (medelvärdet är större än eller lika med) värdet procent. Fältet `value` kan konfigureras efter behov.
- Observera att regeln har ett **operatorfält** som tolkas dynamiskt `AVGGREATEROREQUAL`i frågesyntaxen senare . 
- Regeln filtrerar data på en `2` viss `C1`dimensionsnyckel med värde . Andra fält är tomma stränger, vilket anger att indataströmmen inte filtreras efter dessa händelsefält. Du kan ställa in ytterligare CPU-regler för att filtrera andra matchande fält efter behov.
- Alla kolumner ska inte inkluderas i utdataaviseringshändelsen. I det `includedDim` här `2` fallet är `TRUE` nyckelnummer aktiverat för att representera fältnummer 2 av händelsedata i flödet kommer att inkluderas i de kvalificerande utdatahändelserna. De andra fälten ingår inte i aviseringsutdata, men fältlistan kan justeras.


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

## <a name="example-streaming-query"></a>Exempel på direktuppspelningsfråga
Det här exemplet Stream Analytics-frågan sammanfogar **regelreferensdata** från exemplet ovan, till en indataström med **namngivna mått**.

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

## <a name="example-streaming-input-event-data"></a>Exempel på strömmande indatahändelsedata
Det här exemplet JSON-data representerar de mått indata som används i ovanstående **direktuppspelningsfråga.** 

- Tre exempel händelser listas inom 1-minuters `T14:50`tidsintervall, värde . 
- Alla tre har `deviceId` `978648`samma värde.
- Cpu-måttvärdena varierar `98`i `95` `80` varje händelse, respektive. Endast de två första exempelhändelserna överskrider processoraviseringsregeln som fastställs i regeln.
- Fältet includeDim i aviseringsregeln var nyckelnummer 2. Motsvarande nyckel 2-fält i exempelhändelserna heter `NodeName`. De tre exempelhändelserna `N024`har `N014` värden `N024`, respektive. I utdata ser du bara `N024` noden eftersom det är den enda data som matchar aviseringskriterierna för hög CPU. `N014`uppfyller inte den höga CPU-tröskeln.
- Aviseringsregeln är `filter` konfigurerad med endast på nyckelnummer `cluster` 2, vilket motsvarar fältet i exempelhändelserna. De tre exempelhändelserna `C1` har alla värde och matchar filtervillkoren.

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
I det här exemplet visas JSON-data för att en enda aviseringshändelse har producerats baserat på cpu-tröskelregeln som definierats i referensdata. Utdatahändelsen innehåller namnet på aviseringen samt det aggregerade (medelvärdet, min, max) för de berörda fälten. Utdata händelsedata innehåller fältnyckelnummer 2 `NodeName` värde `N024` på grund av regelkonfigurationen. (JSON ändrades för att visa radbrytningar för läsbarhet.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
