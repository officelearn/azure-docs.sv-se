---
title: 'Scenario: anpassad routning för Azure-brandväggen för virtuellt WAN'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning av routning mellan virtuella nätverk direkt, men Använd Azure-brandväggen för VNet->Internet/gren och gren till VNet-trafikflöden
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f48b30e0e4e76a4cf4c855008776f6b7541ad5a2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850667"
---
# <a name="scenario-azure-firewall---custom"></a>Scenario: Azure Firewall – Custom

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att dirigera trafik mellan virtuella nätverk direkt, men Använd Azure-brandvägg för VNet-till-Internet/gren-och gren-till-VNet-trafikflöden.

## <a name="design"></a><a name="design"></a>Utforma

För att ta reda på hur många väg tabeller som behövs kan du bygga en anslutnings mat ris där varje cell representerar om en källa (rad) kan kommunicera med ett mål (kolumn). Anslutnings matrisen i det här scenariot är trivial, men överensstämmer med andra scenarier. vi kan fortfarande titta på den.

**Anslutnings mat ris**

| Från           | Till:      | *Virtuella nätverk*      | *Grenar*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Virtuella nätverk**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **Grenar**   |   &#8594;|    AzFW      |       X       |       X      |

I den föregående tabellen representerar ett "X" direkt anslutning mellan två anslutningar utan att trafiken passerar Azure-brandväggen i det virtuella WAN-nätverket och "AzFW" visar att flödet kommer att gå via Azure-brandväggen. Eftersom det finns två distinkta anslutnings mönster i matrisen behöver vi två väg tabeller som ska konfigureras på följande sätt:

* Virtuella nätverk:
  * Associerad routningstabell: **RT_VNet**
  * Sprider till routningstabeller: **RT_VNet**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **standard**


Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

I det här scenariot vill du dirigera trafiken via Azure-brandväggen för VNet-till-Internet-, VNet-till-gren-eller gren-till-VNet-trafik, men vill gå direkt för VNet-till-VNet-trafik. Om du använder Azure Firewall Manager fylls väg inställningarna i automatiskt i **standard väg tabellen**. Privat trafik gäller för VNet och filialer, Internet trafik gäller 0.0.0.0/0.

VPN-, ExpressRoute-och användares VPN-anslutningar kallas gemensamt för grenar och associeras till samma (standard) väg tabell. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller. För att konfigurera det här scenariot vidtar du följande steg för att överväga:

1. Skapa en anpassad routningstabell **RT_VNet**.
1. Skapa en väg för att aktivera VNet-till-Internet och VNet-till-gren: 0.0.0.0/0 med nästa hopp som pekar på Azure-brandväggen. I avsnittet spridning ser du till att virtuella nätverk är valt vilka som skulle garantera mer speciella vägar, vilket möjliggör trafik flödet mellan VNet och VNet.

   * I **Association:** Välj virtuella nätverk som innebär att virtuella nätverk når destinationen enligt vägarna för den här väg tabellen.
   * Vid **spridning:** Välj virtuella nätverk som innebär att virtuella nätverk sprids till den här routningstabellen. med andra ord kommer mer angivna vägar att spridas till den här routningstabellen, vilket säkerställer direkt trafikflödet mellan VNet och VNet.

1. Lägg till en aggregerad statisk väg för virtuella nätverk i **standard väg tabellen** om du vill aktivera gren-till-VNet-flödet via Azure-brandväggen.

   * Kom ihåg att grenar är kopplade till och sprider till standard väg tabellen.
   * Grenar sprider inte till RT_VNet routningstabellen. Detta säkerställer trafikflödet för VNet-till-gren via Azure-brandväggen.

Detta leder till konfigurations ändringar i konfigurationen som visas i **bild 1**.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Bild 1":::

> [!NOTE]
> De virtuella WAN-hubbarna och de anslutna virtuella nätverken måste finnas i samma Azure-region.

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
* Mer information om hur du konfigurerar routning för virtuell hubb finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md).
