---
title: ta med fil
description: ta med fil
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: dd678fb784fa7587912f113a7c00bebca76efce8
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857507"
---
Det finns vissa begränsningar för antalet mått och händelser per program, det vill säga per Instrumentation-nyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resource | Standardgräns | Obs!
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. Skicka e-post till för kapaciteter som AIDataCap@microsoft.comär större än 1 000 GB.
| Begränsning | 32 000 händelser/sekund | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) och [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal händelse storlek | 64,000 |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typ scheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Stränglängd för egenskapsvärde | 8 192  | Se [typ scheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Längd för spårnings- och undantagsmeddelande | 32 768  | Se [typ scheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [](../articles/azure-monitor/app/profiler.md) Data kvarhållning för profilering | 5 dagar |
| [](../articles/azure-monitor/app/profiler.md) Profilerade data har skickats per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).