---
title: "Så här skalar du Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här kursen ingår så här skalar du Azure tid serien Insights-miljö"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: ba6bd1ab05bb7e24dd1bc307218e7a772fbde601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Så här skalar du tid serien insikter miljön

Den här kursen ingår så här skalar du tid serien insikter miljön.

> [!NOTE]
> Skala upp över sku-typer tillåts inte. En miljö med en S1 Sku kan inte konverteras till en S2 miljö.

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1-SKU ingång priser och kapacitet

| S1 Artikelnummerkapaciteten | Ingång hastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 1 GB (1 miljon händelser) | 30 GB (30 miljoner händelser) per månad |
| 10 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU ingång priser och kapacitet

| S2 Artikelnummerkapaciteten | Ingång hastighet | Maximal lagringskapacitet
| --- | --- | --- |
| 1 | 10 GB (10 miljoner händelser) | 300 GB (300 miljoner händelser) per månad |
| 10 | 100 GB (100 miljoner händelser) | 3 TB (3 miljarder händelser) per månad |

Kapaciteter skalas linjärt, så en S1 sku med kapacitet 2 stöder 2 GB (2 miljoner) händelser per dag ingång hastighet och 60 GB (60 miljoner händelser) per månad.

## <a name="changing-the-capacity-of-your-environment"></a>Ändra kapaciteten för din miljö

1. Välj miljön vars kapacitet du vill ändra i Azure-portalen.
1. Klicka på Konfigurera under inställningar.
1. Använd skjutreglaget kapacitet för att välja den kapacitet som uppfyller kraven för meddelanden om ingångs-priser och lagringskapacitet.

## <a name="next-steps"></a>Nästa steg

* Kontrollera att den nya kapaciteten är tillräckliga för att förhindra begränsning. Mer information finns i *din miljö kan att komma begränsas* avsnittet [här](time-series-insights-diagnose-and-solve-problems.md).