---
title: 'Scenario: alla-till-alla'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – alla-till-alla
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569027"
---
# <a name="scenario-any-to-any"></a>Scenario: alla-till-alla

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I ett valfritt scenario kan alla ekrar komma åt en annan eker. När det finns flera hubbar är hubb-till-hubb-routning (även kallat mellan hubb) aktiverat som standard i standard Virtual WAN. 

I det här scenariot är VPN-, ExpressRoute-och användares VPN-anslutningar kopplade till samma routningstabell. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller. Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenariots arkitektur

I **bild 1**kan alla virtuella nätverk och grenar (VPN, EXPRESSROUTE, P2s) komma åt varandra. I en virtuell hubb fungerar anslutningarna på följande sätt:

* En VPN-anslutning ansluter en VPN-plats till en VPN-gateway.
* En virtuell nätverks anslutning ansluter ett virtuellt nätverk till en virtuell hubb. Den virtuella hubbens router tillhandahåller överförings funktionerna mellan virtuella nätverk.
* En ExpressRoute-anslutning ansluter en ExpressRoute-krets till en ExpressRoute-Gateway.

Dessa anslutningar (som standard när de skapas) är kopplade till standard väg tabellen, om du inte ställer in Dirigerings konfigurationen för anslutningen till **ingen**, eller en anpassad väg tabell. Dessa anslutningar sprider också vägar som standard till standard väg tabellen. Det här gör det möjligt för ett scenario där alla ekrar (VNet, VPN, ER, P2S) kan komma åt varandra.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="bild 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

Det här scenariot är aktiverat som standard för virtuellt WAN-nätverk. Om inställningen för gren-till-gren är inaktive rad i WAN-konfigurationen kommer det inte att tillåta anslutningar mellan Branch ekrar. VPN/ExpressRoute/User VPN betraktas som Branch ekrar i virtuellt WAN

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
