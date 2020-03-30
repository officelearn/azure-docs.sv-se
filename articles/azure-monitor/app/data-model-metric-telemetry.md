---
title: Datamodell för måtttelemetri - Azure Application Insights
description: Programinsiktsdatamodell för måtttelemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671978"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Måtttelemetri: Datamodell för Application Insights

Det finns två typer av måtttelemetri som stöds av [Application Insights:](../../azure-monitor/app/app-insights-overview.md)en enda mätning och föraggregerade mått. En enda mätning är bara ett namn och värde. Föraggregerat mått anger lägsta och högsta värde för måttet i aggregeringsintervallet och standardavvikelsen för det.

Föraggregerad måtttelemetri förutsätter att aggregeringsperioden var en minut.

Det finns flera välkända måttnamn som stöds av Application Insights. Dessa mått placeras i performanceCounters-tabellen.

Mått som representerar system- och processräknare:

| **.NET-namn**             | **Plattformsagnostikernamn** | **NAMNET PÅ REST API** | **Beskrivning**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pågående arbete... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | total maskin-CPU
| `\Memory\Available Bytes`                 | Pågående arbete... | [minneTillgängligaByten](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Visar mängden fysiskt minne, i byte, tillgängligt för processer som körs på datorn. Den beräknas genom att summera mängden utrymme på listorna nollställt, ledigt och vänteminne. Ledigt minne är klart för användning; nollställt minne består av minnessidor som fyllts med nollor för att förhindra att senare processer ser data som används av en tidigare process. standbyminne är minne som har tagits bort från en processs arbetsminne (dess fysiska minne) på väg till disken men är fortfarande tillgänglig för att återkallas. Se [Minnesobjekt](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pågående arbete... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU för processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pågående arbete... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | minne som används av processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pågående arbete... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | i/o-åtgärder som körs genom process som är värd för programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pågående arbete... | [begärPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | antalet begäranden som behandlas efter ansökan 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pågående arbete... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | andelen undantag som genereras av ansökan
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pågående arbete... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | genomsnittlig körningstid för begäranden
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pågående arbete... | [begärInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | antal begäranden som väntar på bearbetningen i en kö

## <a name="name"></a>Namn

Namnet på det mått som du vill se i application insights-portalen och användargränssnittet. 

## <a name="value"></a>Värde

Ett värde för mätning. Summan av enskilda mätningar för aggregeringen.

## <a name="count"></a>Antal

Måttvikt för det aggregerade måttet. Bör inte ställas in för en mätning.

## <a name="min"></a>Min

Minimivärdet för det aggregerade måttet. Bör inte ställas in för en mätning.

## <a name="max"></a>Max

Maximalt värde för det aggregerade måttet. Bör inte ställas in för en mätning.

## <a name="standard-deviation"></a>Standardavvikelse

Standardavvikelse för det aggregerade måttet. Bör inte ställas in för en mätning.

## <a name="custom-properties"></a>Anpassade egenskaper

Mått med den `CustomPerfCounter` anpassade `true` egenskapen inställd på att ange att måttet representerar windows prestandaräknare. Dessa mått som placeras i tabellen performanceCounters. Inte i customMetrics. Även namnet på det här måttet tolkas för att extrahera kategori-, räknar- och förekomstnamn.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [API för Application Insights för anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
