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
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8e519e5a5e5fe6b893fa5663e1426630f46f885f
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118412"
---
# <a name="event-telemetry-application-insights-data-model"></a>Händelsetelemetri: Application Insights-datamodell

Du kan skapa event telemetri objekt (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) som representerar en händelse som inträffade i ditt program. Det är vanligtvis någon interaktion från användaren som knapp klickar du på eller beställa checka ut. Det kan också vara livscykel händelse som initieras eller konfiguration update. 

Semantiskt, händelser kan eller inte kan korreleras till begäranden. Men om använt korrekt är händelsetelemetri viktigare än begäranden eller spårningar. Händelser som representerar telemetri och bör vara ett ämne att separera, mindre aggressiva [sampling](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Namn

Händelsenamn. Begränsa ditt program för att tillåta korrekt gruppering och användbara mätvärden, så att det genererar ett litet antal separat händelsenamn. Till exempel inte använda ett separat namn för varje genererad instans av en händelse.

Maxlängd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [datamodellen](data-model.md) för Application Insights och modellen.
- [Skriva anpassad händelsetelemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) stöds av Application Insights.
