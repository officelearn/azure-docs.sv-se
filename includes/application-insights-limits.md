---
title: ta med fil
description: ta med fil
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: d253ed7c5aa971f2fd767a6943eb0117c457fc45
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554643"
---
Finns vissa begränsningar för hur många mått och händelser per program, det vill säga per instrumentationsnyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Standardgräns | Obs!
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data du kan höja gränsen i portalen, upp till 1 000 GB. Kapaciteter som är större än 1 000 GB, skickar du e-postmeddelande till AIDataCap@microsoft.com.
| Begränsning | 32 000 händelser/sek | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) och [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximala händelsestorleken | 64,000 | 
| Namnlängd för egenskaper och mätvärden | 150 | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Stränglängd för egenskapsvärde | 8 192 | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Längd för spårnings- och undantagsmeddelande | 10 000 | Se [skriver scheman](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) datakvarhållning | 5 dagar |
| [Profiler](../articles/azure-monitor/app/profiler.md) data som skickas per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).