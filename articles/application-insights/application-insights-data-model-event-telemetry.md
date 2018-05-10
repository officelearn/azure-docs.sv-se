---
title: Datamodell för Azure-program insikter telemetri - händelse telemetri | Microsoft Docs
description: Application Insights datamodellen för händelsen telemetri
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
ms.openlocfilehash: 4f6b5c35b65b4aff2dbe8dafbb2eb07d75c2382a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="event-telemetry-application-insights-data-model"></a>Händelsen telemetri: Application Insights-datamodell

Du kan skapa händelse telemetri objekt (i [Programinsikter](app-insights-overview.md)) som representerar en händelse som påträffats i programmet. Vanligtvis är en användarinteraktion, såsom knappen Klicka eller ordna utcheckningen. Det kan också vara livscykel händelse som initiering eller konfiguration uppdatera. 

Semantiskt, händelser kan eller inte kan korreleras till begäranden. Men om korrekt, är händelse telemetri viktigare än förfrågningar eller spår. Händelser som representerar företag telemetri och bör vara ett ämne att separera, mindre aggressiv [provtagning](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Namn

Händelsenamn. Begränsa ditt program för att tillåta korrekt gruppering och användbara mätvärden, så att den genererar ett litet antal separat händelsenamn. Till exempel inte använda ett separat namn för varje genererade instans av en händelse.

Maxlängd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodellen](application-insights-data-model.md) för Application Insights typer och modell.
- [Skriva anpassade händelsen telemetri](app-insights-api-custom-events-metrics.md#trackevent)
- Checka ut [plattformar](app-insights-platforms.md) stöds av Application Insights.
