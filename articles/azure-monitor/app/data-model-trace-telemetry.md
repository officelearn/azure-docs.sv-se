---
title: Datamodell för Azure Application Insights – spåra telemetri
description: Program Insights datamodell för spårningstelemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671961"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Spåra telemetri: Datamodell för Application Insights

Spårningstelemetri (i Application `printf` Insights ) representerar formatspårningssatser som är textsökda. [Application Insights](../../azure-monitor/app/app-insights-overview.md) `Log4Net`, `NLog`och andra textbaserade loggfilsposter översätts till instanser av den här typen. Spårningen har inga mätningar som utökningsbarhet.

## <a name="message"></a>Meddelande

Spåra meddelande.

Max längd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Spåra allvarlighetsgrad. Värdet kan `Verbose` `Information`vara `Warning` `Error`, `Critical`, , , .

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .NET-spårningsloggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Utforska Java-spårningsloggar i Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- [Skriv anpassad spårningstelemetri](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
