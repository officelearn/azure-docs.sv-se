---
title: Datamodell för Azure Application Insights telemetri – Händelsetelemetri | Microsoft Docs
description: Application Insights-datamodell för händelsetelemetri
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
ms.openlocfilehash: 062478783465edc2d3afa4b80a22f119e68da049
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091714"
---
# <a name="event-telemetry-application-insights-data-model"></a>Händelsetelemetri: Application Insights-datamodell

Du kan skapa event telemetri objekt (i [Application Insights](app-insights-overview.md)) som representerar en händelse som inträffade i ditt program. Det är vanligtvis någon interaktion från användaren som knapp klickar du på eller beställa checka ut. Det kan också vara livscykel händelse som initieras eller konfiguration update. 

Semantiskt, händelser kan eller inte kan korreleras till begäranden. Men om använt korrekt är händelsetelemetri viktigare än begäranden eller spårningar. Händelser som representerar telemetri och bör vara ett ämne att separera, mindre aggressiva [sampling](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Namn

Händelsenamn. Begränsa ditt program för att tillåta korrekt gruppering och användbara mätvärden, så att det genererar ett litet antal separat händelsenamn. Till exempel inte använda ett separat namn för varje genererad instans av en händelse.

Maxlängd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- [Skriva anpassad händelsetelemetri](app-insights-api-custom-events-metrics.md#trackevent)
- Kolla in [plattformar](app-insights-platforms.md) stöds av Application Insights.
