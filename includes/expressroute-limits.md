---
title: inkludera fil
description: inkludera fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 146a257a9b8f31e79283d71ede1e1c5c0355765c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147777"
---
| Resurs | Gräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration, med Azure Resource Manager |10 |
| Maximalt antal vägar som annonseras till Azures privata peering med ExpressRoute standard |4 000 |
| Maximalt antal vägar som annonseras till Azures privata peering med ExpressRoute Premium-tillägg |10 000 |
| Maximalt antal vägar som annonseras från Azures privata peering från VNet-adressutrymmet för en ExpressRoute-anslutning |200 |
| Maximalt antal vägar som annonseras till Microsoft-peering med ExpressRoute standard |200 |
| Maximalt antal vägar som annonseras till Microsoft-peering med ExpressRoute Premium-tillägg |200 |
| Det högsta antal ExpressRoute-kretsar som är länkade till en standard prestanda/ErGw1AZ-Gateway |4 | 
| Det högsta antal ExpressRoute-kretsar länkade till en hög prestanda-/ErGw2AZ-Gateway |8 | 
| Det högsta antal ExpressRoute-kretsar som är länkade till en ultra Performance/ErGw3AZ-Gateway |16 | 
| Maximalt antal ExpressRoute-kretsar som är länkade till samma virtuella nätverk på olika peering-platser (alla SKU: er) |4 |
| Antalet virtuella nätverks länkar som tillåts per ExpressRoute-krets |Se [antalet virtuella nätverk per ExpressRoute-krets](#vnetpercircuit) tabell.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Antal virtuella nätverk per ExpressRoute-krets
| **Krets storlek** | **Antal virtuella nätverks Länkar för standard** | **Antal virtuella nätverks länkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbit/s |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbit/s |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |
| 40 Gbit/s * |10 |100 |
| 100 Gbit/s * |10 |100 |

**100 Gbit/s ExpressRoute Direct endast*

> [!NOTE]
> Global Reach antalet anslutningar mot gränsen för virtuella nätverks anslutningar per ExpressRoute-krets. Till exempel tillåter en 10 Gbit/s Premium-krets 5 Global Reach anslutningar och 95 anslutningar till ExpressRoute-gatewayer eller 95 Global Reach anslutningar och 5 anslutningar till ExpressRoute-Gatewayerna eller någon annan kombination upp till gränsen på 100-anslutningar för kretsen.
