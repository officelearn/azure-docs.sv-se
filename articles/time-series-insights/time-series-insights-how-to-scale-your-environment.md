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
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: ee695798dc8a2a19d5cd3d94cbf43e0b58065f84
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556687"
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

2. I menyn för miljön för Time Series Insighs väljer **konfigurera**.

   ![Configure.PNG](media/scale-your-environment/configure.png)

3. Justera den **kapacitet** skjutreglaget för att välja den kapacitet som uppfyller kraven för dina ingress-priser och lagringskapacitet. Observera den **ingångshändelser**, **lagringskapacitet**, och **uppskattad kostnad** update dynamiskt för att visa effekten av ändringen. 

   ![Skjutreglage](media/scale-your-environment/slider.png)

   Alternativt kan du ange antalet multiplikatorn kapacitet i rutan till höger om skjutreglaget. 

4. Välj **spara** skala miljön. Förloppsindikatorn visas förrän den här ändringen är verkställas under ett ögonblick. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Kontrollera att den nya kapaciteten som är tillräckligt för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).
