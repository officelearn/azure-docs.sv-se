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
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733780"
---
| Resurs | Standard-/ maxgräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration, med Azure Resource Manager |10 |
| Maximalt antal vägar för Azures privata peering med ExpressRoute-Standard |4,000 |
| Maximalt antal vägar för Azures privata peering med ExpressRoute Premium-tillägget |10 000 |
| Maximalt antal vägar för Azures privata peering från virtuella nätverkets adressutrymme för en ExpressRoute-anslutning |200 | 
| Maximalt antal vägar för Microsoft Azure-peering med ExpressRoute-Standard |200 |
| Maximalt antal vägar för Microsoft Azure-peering med ExpressRoute Premium-tillägget |200 |
| Maximalt antal ExpressRoute-kretsar som är länkad till samma virtuella nätverk på samma plats för peering |4 |
| Maximalt antal ExpressRoute-kretsar som är länkad till samma virtuella nätverk i olika peering-platser |> 4, beror på GatewaySubnet storlek|
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

