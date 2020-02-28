---
title: Data modell för Azure Application Insights-undantag
description: Application Insights data modell för telemetri för undantag
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671944"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetri för undantag: Application Insights data modell

I [Application Insights](../../azure-monitor/app/app-insights-overview.md)representerar en instans av undantag ett hanterat eller ohanterat undantag som uppstod under körningen av det övervakade programmet.

## <a name="problem-id"></a>Problem-ID

Identifierare för var undantaget utlöstes i koden. Används för gruppering av undantag. Vanligt vis en kombination av undantags typ och en funktion från anrops stacken.

Maxlängd: 1024 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Allvarlighets grad för spårning. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Undantagsinformation

(Ska utökas)

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Lär dig hur du [diagnostiserar undantag i dina webbappar med Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
