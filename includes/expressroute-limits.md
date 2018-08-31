---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c681e5f68299c03685dd7722f8dc671e49026f78
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43305093"
---
#### <a name="expressroute-limits"></a>Begränsningar för ExpressRoute
Följande begränsningar gäller för ExpressRoute-resurser per prenumeration.

| Resurs | Standardgräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration (Azure Resource Manager) |10 |
| Maximalt antal vägar för Azures privata peering med ExpressRoute-standard |4,000 |
| Maximalt antal vägar för Azures privata peering med Expressroutes premiumtillägg |10 000 |
| Maximalt antal vägar för Azure-Microsoft-peering med ExpressRoute-standard |200 |
| Maximalt antal vägar för Microsoft Azure-peering med Expressroutes premiumtillägg |200 |
| Maximalt antal ExpressRoute-kretsar som är länkad till samma virtuella nätverk i olika peering-platser |4 |
| Antalet virtuella nätverkslänkar som tillåts per ExpressRoute-krets |Se tabellen nedan |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Antalet virtuella nätverk per ExpressRoute-krets
| **Kretsstorlek** | **Antal VNet-länkar för standard** | **Antal VNet-länkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbit/s |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbit/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |

