---
title: Så här skalar du Azure Time Series Insights-miljön | Microsoft Docs
description: Den här artikeln beskriver hur du skalar Azure Time Series Insights-miljön. Använda Azure-portalen för att lägga till eller ta bort kapacitet inom en prisnivå SKU.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f7d9827b11136370bcb401073a4d9cc4871c1cc9
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472794"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Så här skalar du din Time Series Insights-miljö

Den här artikeln beskriver hur du kan ändra kapaciteten för din miljö din Time Series Insights-miljö med Azure portal. Kapaciteten är multiplikatorn som tillämpas på de ingångshändelser, kapacitet och kostnad som hör till din valda SKU.

Du kan använda Azure-portalen för att öka eller minska kapaciteten i en viss prisnivå SKU.

Dock är ändra prisnivån SKU: N inte tillåtet. Till exempel kan inte en miljö med en S1 priser för SKU: N konverteras till en S2 eller vice versa.

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU Ingående frekvens och kapaciteter

| S1 SKU-kapacitet | Ingångshändelser | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 1 GB (1 miljon händelser) | 30 GB (30 miljoner händelser) per månad |
| 10 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU Ingående frekvens och kapaciteter

| S2 SKU-kapacitet | Ingångshändelser | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |
| 10 | 100 GB (100 miljoner händelser) | 3 TB (3 miljarder händelser) per månad |

Kapaciteter skalas linjärt, så en S1 SKU-kapacitet 2 stöder 2 GB (2 miljoner) händelser per dag ingående hastighet och 60 GB (60 miljoner händelser) per månad.

## <a name="change-the-capacity-of-your-environment"></a>Ändra kapaciteten för din miljö

1. Leta upp i Azure-portalen och välj din Time Series Insights-miljö.

1. I menyn för din Time Series Insights-miljö väljer **konfigurera**.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Justera den **kapacitet** skjutreglaget för att välja den kapacitet som uppfyller kraven för dina ingress-priser och lagringskapacitet. Observera den **ingångshändelser**, **lagringskapacitet**, och **uppskattad kostnad** update dynamiskt för att visa effekten av ändringen.

   [![Skjutreglage](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativt kan du ange antalet multiplikatorn kapacitet i rutan till höger om skjutreglaget.

1. Välj **spara** skala miljön. Förloppsindikatorn visas förrän den här ändringen är verkställas under ett ögonblick.

## <a name="next-steps"></a>Nästa steg

- Kontrollera att den nya kapaciteten är [tillräckligt för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).