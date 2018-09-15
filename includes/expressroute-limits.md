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
ms.openlocfilehash: ad75b333fc7d5d37a4ac074727e4d54b67bd0321
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45636938"
---
#### <a name="expressroute-limits"></a>Begränsningar för ExpressRoute
Följande begränsningar gäller för ExpressRoute-resurser per prenumeration.

| Resurs | Standard-/ maxgränsen |
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

