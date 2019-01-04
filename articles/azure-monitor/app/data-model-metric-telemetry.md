---
title: Datamodell för Azure Application Insights telemetri – mått telemetri | Microsoft Docs
description: Application Insights-datamodell för metriska telemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: c365eea0465eab5fec134f270dab41e62cba2f1d
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812452"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrisk telemetri: Application Insights-datamodell

Det finns två typer av mått telemetri som stöds av [Application Insights](../../application-insights/app-insights-overview.md): enkel mätning och preaggregeras mått. Enskild mätning är bara ett namn och värde. Preaggregeras mått anger minsta och högsta värdet för måttet i sammanställningsintervall och standardavvikelsen för den.

Preaggregeras mått telemetri förutsätter att sammanställningsperioden har en minut.

Det finns flera välkända tjänstmåttets namn som stöds av Application Insights. De här måtten placeras i performanceCounters tabell.

Mått som representerar system och processen räknare:

| **.NET-namn**             | **Oberoende plattformsnamn** | **REST API-namn** | **Beskrivning**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pågående arbete... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Totalt antal datorns CPU-användning
| `\Memory\Available Bytes`                 | Pågående arbete... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Visar mängden fysiskt minne i byte, tillgängligt för processer som körs på datorn. Det beräknas genom att summera mängden utrymme på listorna nollställt, ledigt och väntande minne. Ledigt minne är klar. nollställt minne består av satt till nollor, så att senare processer från att se data som används av en tidigare process. vänteläge minne är minne som har tagits bort från en process arbetsminne (dess fysiska minne) på väg till disk men är fortfarande tillgängligt och kan återställas. Se [minnesobjekt](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pågående arbete... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU av processen som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pågående arbete... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | minne som används av den process som är värd för programmet
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pågående arbete... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | frekvensen för i/o-åtgärder som körs av process som är värd för programmet
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pågående arbete... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | antalet begäranden som bearbetas av program 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pågående arbete... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | antal undantag som utlöses av program
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pågående arbete... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | begäranden om Genomsnittlig körningstid
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pågående arbete... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | antalet begäranden som väntar på bearbetning i en kö

## <a name="name"></a>Namn

Namnet på det mått som du skulle vilja se i Application Insights-portalen och Användargränssnittet. 

## <a name="value"></a>Värde

Enskilt värde för mått. Summan av enskilda mått för sammanställning.

## <a name="count"></a>Antal

Tjänstmåttets vikt för sammansatt mått. Ska inte anges för ett mått.

## <a name="min"></a>Min

Minimivärdet för sammansatt mått. Ska inte anges för ett mått.

## <a name="max"></a>Max

Maxvärdet för sammansatt mått. Ska inte anges för ett mått.

## <a name="standard-deviation"></a>Standardavvikelse

Standardavvikelse för sammansatt mått. Ska inte anges för ett mått.

## <a name="custom-properties"></a>Anpassade egenskaper

Mått med den anpassade egenskapen `CustomPerfCounter` inställd `true` indikerar att måtten representerar windows-prestandaräknare. De här måtten placeras i performanceCounters tabell. Inte i customMetrics. Namnet på det här måttet är också parsas för att extrahera kategori, räknare och instansnamn.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [Application Insights API för anpassade händelser och mått](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Se [datamodellen](data-model.md) för Application Insights och modellen.
- Kolla in [plattformar](../../application-insights/app-insights-platforms.md) stöds av Application Insights.
