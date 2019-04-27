---
title: Datamodell för Azure Application Insights telemetri – Spårningstelemetri | Microsoft Docs
description: Application Insights-datamodell för spårningstelemetri
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
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899305"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Spårningstelemetri: Application Insights-datamodell

Spåra telemetri (i [Programinsikter](../../azure-monitor/app/app-insights-overview.md)) representerar `printf` stil spårningsinstruktioner som är text-söks igenom. `Log4Net`, `NLog`, och andra textbaserade loggfilsposter översätts till instanser av den här typen. Spårningen har inte mätning av faktisk användning som en utökningsbarhet.

## <a name="message"></a>Meddelande

Spårningsmeddelande.

Maxlängd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .NET spårningsloggarna i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Utforska spårningsloggar i Application Insights Java](../../azure-monitor/app/java-trace-logs.md).
- Se [datamodellen](data-model.md) för Application Insights och modellen.
- [Skriv anpassad spårningssession telemetri](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) stöds av Application Insights.
