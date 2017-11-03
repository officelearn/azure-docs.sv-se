---
title: "Datamodell för Azure-program insikter telemetri - Undantagstelemetri | Microsoft Docs"
description: "Application Insights datamodellen för undantagstelemetri"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 3c3c3a39c7986cc771fe4baf60ad9b316888f6ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="exception-telemetry-application-insights-data-model"></a>Undantagstelemetri: Application Insights-datamodell

I [Programinsikter](app-insights-overview.md), en instans av undantag representerar en hanterade eller ohanterade undantag som uppstod under körning av det övervakade programmet.

## <a name="problem-id"></a>Problem-Id

Identifierare för där undantaget uppstod i kod. Används för att gruppera undantag. Vanligtvis en kombination av undantagstyp och en funktion från anropsstacken.

Maxlängd: 1024 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Undantagsinformation

(Som ska utökas)

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- Lär dig hur du [diagnostisera undantag i ditt webbprogram med Application Insights](app-insights-asp-net-exceptions.md).
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
