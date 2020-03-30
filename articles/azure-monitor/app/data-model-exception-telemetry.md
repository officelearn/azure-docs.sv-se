---
title: Azure Application Insights Undantag Telemetri Data modell
description: Program insights-datamodell för undantagstelemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671944"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Datamodell för undantagstelemetri: Datamodell för Application Insights

I [Application Insights](../../azure-monitor/app/app-insights-overview.md)representerar en instans av Undantag ett hanterat eller ohanterat undantag som inträffade under körningen av det övervakade programmet.

## <a name="problem-id"></a>Problem-ID

Identifierare för var undantaget har kastats i kod. Används för undantagsgruppering. Vanligtvis en kombination av undantagstyp och en funktion från anropsstacken.

Max längd: 1024 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan `Verbose` `Information`vara `Warning` `Error`, `Critical`, , , .

## <a name="exception-details"></a>Undantagsinformation

(Förlängd)

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- Läs om hur du [diagnostiserar undantag i webbapparna med Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
