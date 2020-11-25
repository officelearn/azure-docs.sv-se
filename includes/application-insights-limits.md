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
ms.openlocfilehash: 76176c72ad77341d7db1c8f4158a90836b74a91c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011359"
---
Det finns vissa begränsningar för antalet mått och händelser per program, det vill säga per Instrumentation-nyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Standardgräns | Anteckning
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. Skicka e-post till för kapaciteter som är större än 1 000 GB AIDataCap@microsoft.com .
| Begränsning | 32 000 händelser/sekund | Gränser är mätt under en minut.
| Datakvarhållning | [30-730 dagar](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/log-query/log-query-overview.md) och [Metrics Explorer](../articles/azure-monitor/platform/metrics-charts.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal storlek för telemetri | 64 kB |
| Maximalt antal telemetri objekt per batch | 64 KB |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Stränglängd för egenskapsvärde | 8 192  | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Längd för spårnings- och undantagsmeddelande | 32 768  | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [Data](../articles/azure-monitor/app/profiler.md) kvarhållning för profilering | 5 dagar |
| [Profilerade](../articles/azure-monitor/app/profiler.md) data har skickats per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).