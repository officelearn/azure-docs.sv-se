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
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 8659efc885be98f42edae0b516ca576e38940c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2018
ms.locfileid: "35568151"
---
#### <a name="expressroute-limits"></a>ExpressRoute-gränser
Följande begränsningar gäller för ExpressRoute-resurser per prenumeration.

| Resurs | Standardgräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration för ARM |10 |
| Maximalt antal vägar för privat Azure-peering med ExpressRoute standard |4,000 |
| Maximalt antal vägar för privat Azure-peering med premium-tillägg för ExpressRoute |10 000 |
| Maximalt antal vägar för offentlig Azure-peering med ExpressRoute standard |200 |
| Maximalt antal vägar för offentlig Azure-peering med premium-tillägg för ExpressRoute |200 |
| Maximalt antal vägar för Microsoft Azure-peering med ExpressRoute standard |200 |
| Maximalt antal vägar för Microsoft Azure-peering med premium-tillägg för ExpressRoute |200 |
| Maximalt antal ExpressRoute-kretsar som är kopplade till samma virtuella nätverk på olika platser för peering |4 |
| Antal virtuella nätverkslänkar som tillåts per ExpressRoute-krets |Se tabellen nedan |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Antalet virtuella nätverk per ExpressRoute-krets
| **Kretsen storlek** | **Antal VNet länkar för standard** | **Antal VNet länkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbit/s |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbit/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |

