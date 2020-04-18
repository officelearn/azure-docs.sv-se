---
title: Skala din miljö – Insikter i Azure Time Series| Microsoft-dokument
description: Lär dig hur du skalar azure time series insights-miljön med hjälp av Azure-portalen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 9604f0c6eeb9d0ac4b7d07f1ab1ba03285fd60fe
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640073"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Så här skalar du time series insights-miljön

I den här artikeln beskrivs hur du ändrar kapaciteten för time series insights-miljön med hjälp av [Azure-portalen](https://portal.azure.com). Kapacitet är multiplikatorn som tillämpas på ingående hastighet, lagringskapacitet och kostnad som är associerad med din valda SKU.

Du kan använda Azure-portalen för att öka eller minska kapaciteten inom en viss pris-SKU.

Det är dock inte tillåtet att ändra prisnivån SKU. En miljö med S1-pris-SKU kan till exempel inte konverteras till en S2 eller vice versa.

## <a name="ga-limits"></a>GA-gränser

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Ändra kapaciteten i din miljö

1. I Azure-portalen letar du reda på och väljer din Time Series Insights-miljö.

1. Välj **Lagringskonfiguration**på menyn för time series insights-miljön .

   [![Konfigurera kapaciteten för time series-insikter](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Justera **skjutreglaget Kapacitet** för att välja den kapacitet som uppfyller kraven för dina inträngningshastigheter och lagringskapacitet. Lägg märke till **ingående hastighet,** **lagringskapacitet**och **beräknad kostnadsuppdatering** dynamiskt för att visa effekten av ändringen.

   [![Konfigurera din miljö med hjälp av kapacitetsreglaget](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Du kan också skriva numret på kapacitetsmultiplikatorn i textrutan till höger om skjutreglaget.

1. Välj **Spara** om du vill skala miljön. Förloppsindikatorn visas tills ändringen har genomförts, tillfälligt.

1. Kontrollera att den nya kapaciteten är [tillräcklig för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i Förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).

- Lär dig mer om hur du [konfigurerar datalagring i Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Läs mer om [att planera din miljö.](time-series-insights-environment-planning.md)
