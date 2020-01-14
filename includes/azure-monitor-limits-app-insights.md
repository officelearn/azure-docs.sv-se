---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c68e58bb18a5dba07855234af07b0a8be767bed0
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927928"
---
Det finns vissa begränsningar för antalet mått och händelser per program, det vill säga per Instrumentation-nyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Standardgräns | Obs!
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. Skicka e-post till AIDataCap@microsoft.comför kapaciteter som är större än 1 000 GB.
| Begränsning | 32 000 händelser/sekund | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) och [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal händelse storlek | 64 000 000 byte |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typ scheman](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Stränglängd för egenskapsvärde | 8 192 | Se [typ scheman](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Längd för spårnings- och undantagsmeddelande | 32 768  | Se [typ scheman](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [Data](../articles/azure-monitor/app/profiler.md) kvarhållning för profilering | 5 dagar |
| [Profilerade](../articles/azure-monitor/app/profiler.md) data har skickats per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).
