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
ms.openlocfilehash: 1fc63dc53d61a8b2e26f97cc09a359b3f2c7665c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901948"
---
| Resurs | Standard/högsta gräns |
| --- | --- |
| ExpressRoute-kretsar per prenumeration |10 |
| ExpressRoute-kretsar per region per prenumeration, med Azure Resource Manager |10 |
| Maximalt antal vägar som annonseras till Azures privata peering med ExpressRoute standard |4,000 |
| Maximalt antal vägar som annonseras till Azures privata peering med ExpressRoute Premium-tillägg |10 000 |
| Maximalt antal vägar som annonseras från Azures privata peering från VNet-adressutrymmet för en ExpressRoute-anslutning |200 |
| Maximalt antal vägar som annonseras till Microsoft-peering med ExpressRoute standard |200 |
| Maximalt antal vägar som annonseras till Microsoft-peering med ExpressRoute Premium-tillägg |200 |
| Maximalt antal ExpressRoute-kretsar länkade till samma virtuella nätverk på samma peering-plats |4 |
| Maximalt antal ExpressRoute-kretsar som är länkade till samma virtuella nätverk på olika peering-platser |4 |
| Antalet virtuella nätverks länkar som tillåts per ExpressRoute-krets |Se [antalet virtuella nätverk per ExpressRoute-krets](#vnetpercircuit) tabell.  |

#### <a name="vnetpercircuit"></a>Antal virtuella nätverk per ExpressRoute-krets
| **Krets storlek** | **Antal virtuella nätverks Länkar för standard** | **Antal virtuella nätverks länkar med Premium-tillägg** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbit/s |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbit/s |10 |50 |
| 2 Gbit/s |10 |60 |
| 5 Gbit/s |10 |75 |
| 10 Gbit/s |10 |100 |
| 40 Gbit/s * |10 |100 |
| 100 Gbit/s * |10 |100 |

**100 Gbit/s ExpressRoute Direct*

> [!NOTE]
> Global Reach antalet anslutningar mot gränsen för virtuella nätverks anslutningar per ExpressRoute-krets. Till exempel tillåter en 10 Gbit/s Premium-krets 5 Global Reach anslutningar och 95 anslutningar till ExpressRoute-gatewayer eller 95 Global Reach anslutningar och 5 anslutningar till ExpressRoute-gatewayer eller någon annan kombination upp till gränsen på 100-anslutningar för kretsen.
