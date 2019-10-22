---
title: Data modell för Azure Application Insights-telemetri – Event-telemetri | Microsoft Docs
description: Application Insights data modell för telemetri av händelser
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 620983fb2174fe7f32a61503b0b87e8e7ce0c330
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678155"
---
# <a name="event-telemetry-application-insights-data-model"></a>Event telemetri: Application Insights data modell

Du kan skapa objekt för telemetri (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) för att representera en händelse som har inträffat i ditt program. Vanligt vis är det en användar interaktion som knapp tryckning eller order utcheckning. Det kan också vara ett programs livs cykel händelse som initiering eller konfigurations uppdatering. 

Semantiskt, händelser kan eventuellt inte korreleras med begär Anden. Men om det används korrekt är event telemetri viktigare än begär Anden eller spårningar. Händelser representerar affärstelemetri och bör vara föremål för separat, mindre aggressiv [sampling](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Namn

Händelse namn. Begränsa ditt program så att det genererar ett litet antal separata händelse namn för att tillåta rätt gruppering och användbara mått. Använd till exempel inte ett separat namn för varje genererad instans av en händelse.

Maxlängd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [data modell](data-model.md) för Application Insights typer och data modell.
- [Skriv anpassad telemetri för händelser](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
