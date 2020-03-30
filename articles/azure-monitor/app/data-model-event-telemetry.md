---
title: Telemetridatamodell för Azure Application Insights – händelsetelemetri | Microsoft-dokument
description: Programinsiktsdatamodell för händelsetelemetri
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671893"
---
# <a name="event-telemetry-application-insights-data-model"></a>Händelsetelemetri: Datamodell för Application Insights

Du kan skapa händelsetelemetriobjekt (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) för att representera en händelse som inträffade i ditt program. Vanligtvis är det en användarinteraktion som knappklick eller orderutcheckning. Det kan också vara en programlivscykelhändelse som initiering eller konfigurationsuppdatering. 

Semantiskt kan händelser korreleras med förfrågningar. Om händelsetelemetri används på rätt sätt är det dock viktigare än begäranden eller spårningar. Händelser representerar affärstelemetri och bör vara föremål för separat, mindre aggressiv [sampling](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Namn

Händelsenamn. Om du vill tillåta korrekt gruppering och användbara mått begränsar du programmet så att det genererar ett litet antal separata händelsenamn. Använd till exempel inte ett separat namn för varje genererad förekomst av en händelse.

Max längd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodell](data-model.md) för programinsiktstyper och datamodell.
- [Skriv anpassad händelsetelemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
