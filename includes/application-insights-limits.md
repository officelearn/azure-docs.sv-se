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
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982611"
---
Det finns vissa gränser för antalet mått och händelser per program, det vill ha per instrumenteringsnyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Standardgräns | Obs!
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. För kapaciteter som är större än 1 AIDataCap@microsoft.com000 GB skickar du e-post till .
| Begränsning | 32 000 evenemang/sekund | Gränser är mätt under en minut.
| Datakvarhållning | [30 - 730 dagar](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) och [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal storlek på telemetriobjekt | 64 kB |
| Maximala telemetriartiklar per batch | 64 K |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typscheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Stränglängd för egenskapsvärde | 8 192  | Se [typscheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Längd för spårnings- och undantagsmeddelande | 32,768  | Se [typscheman](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| Lagring av [profilerade](../articles/azure-monitor/app/profiler.md) data | 5 dagar |
| [Profilerade](../articles/azure-monitor/app/profiler.md) data som skickas per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).