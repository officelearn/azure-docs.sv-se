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
ms.openlocfilehash: fa448975a5275e5aab4dc82b75433d188422e507
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554197"
---
#### <a name="expressroute-limits"></a>Begränsningar för ExpressRoute
Följande begränsningar gäller för Azure ExpressRoute-resurser per prenumeration.

| Resurs | Standard-/ maxgräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration, med Azure Resource Manager |10 |
| Maximalt antal vägar för Azures privata peering med ExpressRoute-Standard |4,000 |
| Maximalt antal vägar för Azures privata peering med ExpressRoute Premium-tillägget |10 000 |
| Maximalt antal vägar för Microsoft Azure-peering med ExpressRoute-Standard |200 |
| Maximalt antal vägar för Microsoft Azure-peering med ExpressRoute Premium-tillägget |200 |
| Maximalt antal ExpressRoute-kretsar som är länkad till samma virtuella nätverk i olika peering-platser |4 |
| Antalet virtuella nätverkslänkar som tillåts per ExpressRoute-krets |Se tabellen nedan. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Antalet virtuella nätverk per ExpressRoute-krets
| **Kretsstorlek** | **Antalet virtuella nätverkslänkar för Standard** | **Antalet virtuella nätverkslänkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbit/s |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbit/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |

