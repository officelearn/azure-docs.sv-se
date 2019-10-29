---
title: Så här skalar du Azure Time Series Insightss miljön | Microsoft Docs
description: 'I den här artikeln beskrivs hur du skalar din Azure Time Series Insightss miljö. Använd Azure Portal för att lägga till eller ta bort kapacitet inom pris-SKU: n.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991172"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Så här skalar du Time Series Insightss miljön

I den här artikeln beskrivs hur du ändrar kapaciteten för din Time Series Insights miljö med hjälp av [Azure Portal](https://portal.azure.com). Kapaciteten är multiplikatorn som tillämpas på ingångs pris, lagrings kapacitet och kostnad som är kopplad till den valda SKU: n.

Du kan använda Azure Portal för att öka eller minska kapaciteten inom en specifik pris-SKU.

Det är dock inte tillåtet att ändra pris nivåns SKU. Till exempel kan en miljö med en pris-SKU för S1-prissättning inte konverteras till en S2 eller vice versa.

## <a name="ga-limits"></a>GA-gränser

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Ändra kapaciteten för din miljö

1. Leta upp och välj din Time Series Insights-miljö i Azure Portal.

1. Välj **Konfigurera**i menyn för din Time Series Insights miljö.

   [![konfigurera. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Justera skjutreglaget **kapacitet** för att välja den kapacitet som uppfyller kraven för ingångs frekvens och lagrings kapacitet. Observera ingångs **frekvens**, **lagrings kapacitet**och **Beräknad kostnads** uppdatering dynamiskt för att visa effekten av ändringen.

   [![skjutreglage](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativt kan du ange antalet kapacitets multiplikatorer i text rutan till höger om skjutreglaget.

1. Välj **Spara** för att skala miljön. Förlopps indikatorn visas tills ändringen är genomförd, tillfälligt.

1. Kontrol lera att den nya kapaciteten [räcker för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).

- Läs mer om hur du [konfigurerar datakvarhållning i Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Lär dig [att planera ut din miljö](time-series-insights-environment-planning.md).