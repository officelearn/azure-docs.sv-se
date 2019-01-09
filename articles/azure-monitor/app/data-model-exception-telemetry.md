---
title: Datamodell för Azure Application Insights telemetri – Undantagstelemetri | Microsoft Docs
description: Application Insights-datamodell för undantagstelemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: efd7ad43ee9a2206f474621612eca7dfe5079f99
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117104"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Undantagstelemetri: Application Insights-datamodell

I [Application Insights](../../azure-monitor/app/app-insights-overview.md), en instans av undantag representerar en hanterade eller ohanterade undantag som uppstod under körning av det övervakade programmet.

## <a name="problem-id"></a>Problem-ID

Identifierare för där undantaget uppstod i kod. Används för undantag som gruppering. Vanligtvis en kombination av Undantagstypen och en funktion i anropsstacken.

Maxlängd: 1024 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Undantagsinformation

(För att utökas)

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodellen](data-model.md) för Application Insights och modellen.
- Lär dig hur du [diagnostisera undantag i dina webbappar med Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) stöds av Application Insights.
