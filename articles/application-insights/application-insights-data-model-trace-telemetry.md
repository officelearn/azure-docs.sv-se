---
title: Datamodell för Azure-program insikter telemetri - Spårningstelemetri | Microsoft Docs
description: Application Insights datamodellen för spårningstelemetri
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: d93ed9f292b6c05d0a3fb3202567f4024f62e35e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="trace-telemetry-application-insights-data-model"></a>Spåra telemetri: Application Insights-datamodell

Spåra telemetri (i [Programinsikter](app-insights-overview.md)) representerar `printf` style trace-satser som är text som genomsöks. `Log4Net`, `NLog`, och andra textbaserade loggfilsposter översätts till instanser av den här typen. Spårningen har inte mätningar som en utökning.

## <a name="message"></a>Meddelande

Spåra meddelande.

Maxlängd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .NET spårningsloggar i Application Insights](app-insights-asp-net-trace-logs.md).
- [Utforska Java spårningsloggar i Application Insights](app-insights-java-trace-logs.md).
- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- [Skriva anpassade spårningstelemetri](app-insights-api-custom-events-metrics.md#tracktrace)
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
