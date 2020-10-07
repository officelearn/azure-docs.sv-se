---
title: inkludera fil
description: inkludera fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: eaf2c2e2c1954ae848c280adf4f1ae81ffc79ee8
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779078"
---
Det finns vissa begränsningar för antalet mått och händelser per program, det vill säga per Instrumentation-nyckel. Gränserna beror på vilken [prisplan](https://azure.microsoft.com/pricing/details/application-insights/) du väljer.

| Resurs | Gräns | Obs!
| --- | --- | --- |
| Totala data per dag | 100 GB | Du kan minska datamängden genom att ange ett tak. Om du behöver mer data kan du öka gränsen i portalen, upp till 1 000 GB. Skicka e-post till för kapaciteter som är större än 1 000 GB AIDataCap@microsoft.com .
| Begränsning | 32 000 händelser/sekund | Gränser är mätt under en minut.
| Datakvarhållning | 90 dagar | Den här resursen är för [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) och [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Flerstegstest för tillgänglighet](../articles/azure-monitor/app/availability-multistep.md) (kvarhållning av detaljerade resultat) | 90 dagar | Den här resursen innehåller detaljerade resultat för varje steg.
| Maximal händelse storlek | 64 000 000 byte |
| Namnlängd för egenskaper och mätvärden | 150 | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Stränglängd för egenskapsvärde | 8 192 | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Längd för spårnings- och undantagsmeddelande | 32 768  | Se [typ scheman](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| [Tillgänglighetstester](../articles/azure-monitor/app/monitor-web-app-availability.md) (antal per app) | 100 |
| [Data](../articles/azure-monitor/app/profiler.md) kvarhållning för profilering | 5 dagar |
| [Profilerade](../articles/azure-monitor/app/profiler.md) data har skickats per dag | 10 GB |

Mer information finns i [Om priser och kvoter i Application Insights](../articles/azure-monitor/app/pricing.md).
