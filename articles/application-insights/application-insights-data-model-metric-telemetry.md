---
title: Datamodell för Azure-program insikter telemetri - mått telemetri | Microsoft Docs
description: Application Insights datamodellen för mått telemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 3feb1e79ba4e4666cbe9ffc77136520922728710
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="metric-telemetry-application-insights-data-model"></a>Mått telemetri: Application Insights-datamodell

Det finns två typer av mått telemetri som stöds av [Programinsikter](app-insights-overview.md): enkel mätning och före aggregerade mått. Enskild mätning är bara ett namn och värde. Före aggregerade mått anger lägsta och högsta värdet för måttet i aggregeringsintervall och standardavvikelsen för den.

Före aggregerade mått telemetri förutsätter aggregering perioden har en minut.

Det finns flera välkända mått namn som stöds av Application Insights. De här måtten placeras i prestandaräknarna tabell.

Måttet som representerar system och processen räknare:

| **.NET-namn**             | **Plattform storleksoberoende namn** | **REST API-namn** | **Beskrivning**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pågående arbete... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | totala antalet datorer CPU
| `\Memory\Available Bytes`                 | Pågående arbete... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Visar mängden fysiskt minne i byte för processer som körs på datorn. Detta beräknas genom att summera mängden utrymme på listorna över nollställt, ledigt och väntande minne. Ledigt minne är klar. nollställt minne består av minnessidor som fyllts med nollor för att senare processer från att visa data som används av en tidigare process. väntande minne är minne som har tagits bort från arbetsminnet för en process (dess fysiska minne) under transport till disk, men är fortfarande tillgängligt och kan återställas. Se [minnesobjekt](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pågående arbete... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Processor för processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pågående arbete... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | minne som används av processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pågående arbete... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | antalet i/o-åtgärder körs med processen som är värd för programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pågående arbete... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | antalet begäranden som bearbetas av programmet 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pågående arbete... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | antal undantag som utlöses av program
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pågående arbete... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | begäranden om Genomsnittlig körningstid
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pågående arbete... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | antalet begäranden som väntar på bearbetning i en kö

## <a name="name"></a>Namn

Namnet på måttet som du skulle vilja se i Application Insights-portalen och Användargränssnittet. 

## <a name="value"></a>Värde

Enskilt värde för mått. Summan av enskilda mått för aggregering.

## <a name="count"></a>Antal

Tjänstmåttets vikt av sammanställda mått. Ska inte anges för ett mått.

## <a name="min"></a>Min

Minimivärdet för aggregerade mått. Ska inte anges för ett mått.

## <a name="max"></a>Max.

Maximalt värde av sammanställda mått. Ska inte anges för ett mått.

## <a name="standard-deviation"></a>Standardavvikelse

Standardavvikelsen för aggregerade mått. Ska inte anges för ett mått.

## <a name="custom-properties"></a>Anpassade egenskaper

Måttet med den anpassade egenskapen `CustomPerfCounter` inställd på `true` indikerar att måttet representerar windows-prestandaräknare. De här måtten placeras i prestandaräknarna tabell. Inte i customMetrics. Namnet på det här måttet är också parsa extrahera kategori, prestandaräknare och instansnamn.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [Application Insights API för anpassade händelser och mått](app-insights-api-custom-events-metrics.md#trackmetric).
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
