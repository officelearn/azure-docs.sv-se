---
title: 'Scenario: anpassad isolering för virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – förhindra att valda virtuella nätverk kan komma åt varandra
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569018"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: anpassad isolering för virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I ett anpassat isolerings scenario för virtuella nätverk är målet att förhindra att en speciell uppsättning virtuella nätverk kan komma åt en annan speciell uppsättning virtuella nätverk. Virtuella nätverk krävs dock för att komma åt alla grenar (VPN/ER/användare VPN).

I det här scenariot är VPN-, ExpressRoute-och användares VPN-anslutningar (gemensamt kallade grenar) kopplade till samma routningstabell (standard väg tabell). Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller. Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Scenario arbets flöde

I **bild 1**finns det blå och röda VNet-anslutningar.

* Blue-anslutna virtuella nätverk kan komma åt varandra, samt komma åt alla grenar (VPN/ER/P2S)-anslutningar.
* Red virtuella nätverk kan komma åt varandra, samt komma åt alla grenar (VPN/ER/P2S)-anslutningar.

Överväg följande steg när du konfigurerar routning.

1. Skapa två anpassade väg tabeller i Azure Portal **RT_BLUE** och **RT_RED**.
2. För routningstabellen **RT_BLUE**under:
   * **Association**: Markera alla blå virtuella nätverk
   * **Spridning**: för grenar väljer du alternativet för grenar, vilket innebär att förgreningar (VPN/er/P2s)-anslutningar sprider vägar till den här routningstabellen.
3. Upprepa samma steg för **RT_RED** routningstabell för Red virtuella nätverk och grenar (VPN/er/P2s).

Detta leder till att konfigurations konfigurationen ändras så som visas i figuren nedan

**Bild 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="bild 1":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
