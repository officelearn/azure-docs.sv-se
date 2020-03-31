---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335110"
---
| Resurs | Gräns |
| --- | --- |
| ExpressRoute kretsar per abonnemang |10 |
| ExpressRoute kretsar per region per prenumeration, med Azure Resource Manager |10 |
| Maximalt antal vägar som annonseras till Azure-privat peering med ExpressRoute Standard |4 000 |
| Maximalt antal vägar som annonseras till Azure-privat peering med ExpressRoute Premium-tillägg |10 000 |
| Maximalt antal vägar som annonseras från azure-privat peering från VNet-adressutrymmet för en ExpressRoute-anslutning |200 |
| Maximalt antal vägar som annonseras till Microsoft-peering med ExpressRoute Standard |200 |
| Maximalt antal rutter som annonseras till Microsoft-peering med ExpressRoute Premium-tillägg |200 |
| Maximalt antal ExpressRoute-kretsar som är länkade till samma virtuella nätverk på samma peering-plats |4 |
| Maximalt antal ExpressRoute-kretsar som är länkade till samma virtuella nätverk på olika peering-platser |4 |
| Antal virtuella nätverkslänkar som tillåts per ExpressRoute-krets |Se [tabellen Antal virtuella nätverk per ExpressRoute-kretstabell.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Antal virtuella nätverk per ExpressRoute-krets
| **Kretsstorlek** | **Antal virtuella nätverkslänkar för Standard** | **Antal virtuella nätverkslänkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |
| 40 Gbit/s* |10 |100 |
| 100 Gbit/s* |10 |100 |

**Endast 100 Gbit/s ExpressRoute Direct*

> [!NOTE]
> Global Reach-anslutningar räknas mot gränsen för virtuella nätverksanslutningar per ExpressRoute Circuit. En Premium-krets på 10 Gbit/s skulle till exempel tillåta 5 Global Reach-anslutningar och 95 anslutningar till ExpressRoute Gateways eller 95 Global Reach-anslutningar och 5 anslutningar till ExpressRoute Gateways eller någon annan kombination upp till gränsen på 100 anslutningar för kretsen.
