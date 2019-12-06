---
title: Data modell för Azure Application Insights-undantag
description: Application Insights data modell för telemetri för undantag
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 7decac2e5e65ff7eedb374858fe15360b4a1b2c5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872629"
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
