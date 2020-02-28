---
title: Azure Application Insights data modell – trace telemetri
description: Application Insights data modell för trace-telemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671961"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Trace telemetri: Application Insights data modell

Trace telemetri (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representerar `printf` stil spårnings satser som är text-genomsökta. `Log4Net`, `NLog`och andra textbaserade logg fils poster översätts till instanser av den här typen. Spårningen har inte några mått som utöknings barhet.

## <a name="message"></a>Meddelande

Spårnings meddelande.

Maxlängd: 32768 tecken

## <a name="severity-level"></a>Allvarlighetsgrad

Allvarlighets grad för spårning. Värdet kan vara `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nästa steg

- [Utforska .net-spårnings loggar i Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Utforska Java trace-loggar i Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- [Skriv anpassad trace-telemetri](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
