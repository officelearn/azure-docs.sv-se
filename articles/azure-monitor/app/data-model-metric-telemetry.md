---
title: Azure Application Insights telemetri data modell-mått telemetri | Microsoft Docs
description: Application Insights data modell för telemetri av mått
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 816fa37ea052b18dab80bcc0d5c1528cd3d9a014
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678130"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Mått telemetri: Application Insights data modell

Det finns två typer av mått telemetri som stöds av [Application Insights](../../azure-monitor/app/app-insights-overview.md): enstaka mått och föraggregerade mått. Enstaka mått är bara ett namn och ett värde. Föraggregerade mått anger det lägsta och högsta värdet för måttet i agg regerings intervallet och standard avvikelsen för det.

Telemetri för föraggregerade mått förutsätter att agg regerings perioden är en minut.

Det finns flera välkända mått namn som stöds av Application Insights. Dessa mått placeras i performanceCounters-tabellen.

Mått som representerar system-och process räknare:

| **.NET-namn**             | **Plattformens oberoende namn** | **REST API namn** | **Beskrivning**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pågående arbete... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Total maskin-CPU
| `\Memory\Available Bytes`                 | Pågående arbete... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Visar mängden fysiskt minne, i byte, som är tillgängligt för processer som körs på datorn. Det beräknas genom att summan av utrymmet på minnes listorna nollställs, fri och standby används. Ledigt minne är klart att använda. nollställt minne består av sidor med minne som är fyllda med nollor för att förhindra senare processer från att se data som används i en tidigare process. standby-minne är minne som har tagits bort från en processs aktiva sidmängd (dess fysiska minne) en väg till disk, men är fortfarande tillgängligt för att återkallas. Se [minnes objekt](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pågående arbete... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU för processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pågående arbete... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | minne som används av processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pågående arbete... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | antalet I/O-åtgärder som körs genom processen som är värd för programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pågående arbete... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | antal begär Anden som bearbetas av programmet 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pågående arbete... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frekvens av undantag som har utlösts av programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pågående arbete... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | genomsnittlig körnings tid för begär Anden
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pågående arbete... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | antal begär Anden som väntar på bearbetning i en kö

## <a name="name"></a>Namn

Namnet på det mått som du vill se i Application Insights Portal och användar gränssnitt. 

## <a name="value"></a>Värde

Enskilt värde för mått. Summan av enskilda mått för aggregation.

## <a name="count"></a>Antal

Mått vikt för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="min"></a>Min

Minsta värde för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="max"></a>Max.

Högsta värde för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="standard-deviation"></a>Standardavvikelse

Standard avvikelsen för det aggregerade måttet. Ska inte anges för ett mått.

## <a name="custom-properties"></a>Anpassade egenskaper

Måttet med den anpassade egenskapen `CustomPerfCounter` inställt på `true` anger att måttet representerar Windows prestanda räknare. Dessa mått placeras i performanceCounters-tabellen. Inte i customMetrics. Dessutom parsas namnet på det här måttet för att extrahera kategori, räknare och instans namn.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [Application Insights API för anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
