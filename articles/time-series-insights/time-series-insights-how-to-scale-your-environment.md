---
title: Så här skalar du Azure Time Series Insightss miljön | Microsoft Docs
description: 'I den här artikeln beskrivs hur du skalar din Azure Time Series Insightss miljö. Använd Azure Portal för att lägga till eller ta bort kapacitet inom pris-SKU: n.'
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: a899de22137decc1eb1578369a2751710c17abda
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332852"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Så här skalar du Time Series Insightss miljön

I den här artikeln beskrivs hur du ändrar kapaciteten för din Time Series Insights miljö med hjälp av [Azure Portal](https://portal.azure.com). Kapaciteten är multiplikatorn som tillämpas på ingångs pris, lagrings kapacitet och kostnad som är kopplad till den valda SKU: n.

Du kan använda Azure Portal för att öka eller minska kapaciteten inom en specifik pris-SKU.

Det är dock inte tillåtet att ändra pris nivåns SKU. Till exempel kan en miljö med en pris-SKU för S1-prissättning inte konverteras till en S2 eller vice versa.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Priser och kapaciteter för S1-SKU

| S1 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 1 GB (1 000 000 händelser) | 30 GB (30 000 000 händelser) per månad |
| 10 | 10 GB (10 000 000 händelser) | 300 GB (300 000 000 händelser) per månad |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU-ingångs frekvens och kapacitet

| S2 SKU-kapacitet | Ingress-pris | Maximal lagrings kapacitet
| --- | --- | --- |
| 1 | 10 GB (10 000 000 händelser) | 300 GB (300 000 000 händelser) per månad |
| 10 | 100 GB (100 000 000 händelser) | 3 TB (3 000 000 000 händelser) per månad |

Kapaciteter skalas linjärt, så en S1-SKU med kapacitet 2 stöder 2 2 000 000 GB händelser per dag och 60 GB (60 000 000-händelser) per månad.

## <a name="change-the-capacity-of-your-environment"></a>Ändra kapaciteten för din miljö

1. Leta upp och välj din Time Series Insights-miljö i Azure Portal.

1. Välj **Konfigurera**i menyn för din Time Series Insights miljö.

   [@no__t -1configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Justera skjutreglaget **kapacitet** för att välja den kapacitet som uppfyller kraven för ingångs frekvens och lagrings kapacitet. Observera ingångs **frekvens**, **lagrings kapacitet**och **Beräknad kostnads** uppdatering dynamiskt för att visa effekten av ändringen.

   [![Slider](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativt kan du ange antalet kapacitets multiplikatorer i text rutan till höger om skjutreglaget.

1. Välj **Spara** för att skala miljön. Förlopps indikatorn visas tills ändringen är genomförd, tillfälligt.

1. Kontrol lera att den nya kapaciteten [räcker för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Time Series Insights](time-series-insights-concepts-retention.md).

- Läs mer om hur du [konfigurerar datakvarhållning i Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Lär dig [att planera ut din miljö](time-series-insights-environment-planning.md).