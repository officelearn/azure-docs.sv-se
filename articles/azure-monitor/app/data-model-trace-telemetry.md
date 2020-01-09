---
title: Azure Application Insights data modell – trace telemetri
description: Application Insights data modell för trace-telemetri
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407142"
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
