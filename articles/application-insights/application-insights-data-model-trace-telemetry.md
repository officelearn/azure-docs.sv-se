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
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 2487df93baec80c0e4f7741aa7b052ac89459b3b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648786"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Spåra telemetri: Application Insights-datamodell

Spåra telemetri (i [Programinsikter](app-insights-overview.md)) representerar `printf` stil spårningsinstruktioner som är text-söks igenom. `Log4Net`, `NLog`, och andra textbaserade loggfilsposter översätts till instanser av den här typen. Spårningen har inte mätning av faktisk användning som en utökningsbarhet.

## <a name="message"></a>Meddelande

Spårningsmeddelande.

Maxlängd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .NET spårningsloggarna i Application Insights](app-insights-asp-net-trace-logs.md).
- [Utforska spårningsloggar i Application Insights Java](app-insights-java-trace-logs.md).
- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- [Skriv anpassad spårningssession telemetri](app-insights-api-custom-events-metrics.md#tracktrace)
- Kolla in [plattformar](app-insights-platforms.md) stöds av Application Insights.
