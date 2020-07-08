---
title: 'Scenario: dirigera trafik via NVA med anpassade inställningar'
titleSuffix: Azure Virtual WAN
description: Det här scenariot hjälper dig att dirigera trafik via NVA med en annan NVA för Internet-baserad trafik.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569267"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: dirigera trafik genom NVA – anpassad (för hands version)

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I den här NVA-scenariot (Network Virtual installation) är målet att dirigera trafik genom en NVA för VNet <-> gren och använda en annan NVA för Internet-baserad trafik. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenariots arkitektur

I **bild 1**finns det en hubb, **hubb 1**.

* **Hubb 1** är direkt ansluten till NVA virtuella nätverk **VNet 4** och **VNet 5**.

* Trafik mellan virtuella nätverk 1, 2 och 3 och grenar (VPN/ER/P2S) förväntas gå via **VNet 4 NVA** 10.4.0.5.

* All Internet bindnings trafik från virtuella nätverk 1, 2 och 3 förväntas gå via **VNet 5 NVA** 10.5.0.5.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Bild 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

Om du vill konfigurera routning via NVA följer du stegen nedan:

1. För att Internet-bunditnde trafik ska gå via VNET5 behöver du virtuella nätverk 1, 2 och 3 för att ansluta via VNet-peering till VNET 5 direkt. Du behöver också en UDR som kon figurer ATS i virtuella nätverk för 0.0.0.0/0 och nästa hopp 10.5.0.5. För närvarande tillåter inte Virtual WAN en nästa hopp-NVA i den virtuella hubben för 0.0.0.0/0.

1. I Azure Portal navigerar du till den virtuella hubben och skapar en anpassad väg tabell **RT_Shared** som kommer att lära sig vägar via spridning från alla virtuella nätverk-och förgrenings anslutningar. I **bild 2**visas detta som en tom anpassad väg tabell **RT_Shared**.

   * **Vägar:** Du behöver inte lägga till några statiska vägar.

   * **Association:** Välj virtuella nätverk 4 och 5, vilket innebär att virtuella nätverk 4 och 5 anslutningar är kopplade till routningstabellen **RT_Shared**.

   * **Spridning:** Eftersom du vill att alla grenar och VNet-anslutningar ska sprida sina vägar dynamiskt till den här routningstabellen väljer du grenar och alla virtuella nätverk.

1. Skapa en anpassad routningstabell **RT_V2B** för att dirigera trafik från virtuella nätverk 1, 2 och 3 till grenar.

   * **Vägar:** Lägg till en sammanställd statisk väg post för grenar (VPN/ER/P2S) (10.2.0.0/16 i **bild 2**) med nästa hopp som VNet 4-anslutningen. Du måste också konfigurera en statisk väg i VNET 4-anslutning för förgrenings-prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNET 4.

   * **Association:** Välj alla virtuella nätverk 1, 2 och 3. Detta innebär att VNet-anslutningarna 1, 2 och 3 associeras med denna routningstabell och kan lära sig vägar (statiskt och dynamiskt via spridning) i den här routningstabellen.

   * **Spridning:** Anslutningar sprider vägar till routningstabeller. Om du väljer virtuella nätverk 1, 2 och 3 aktive ras spridning av vägar från virtuella nätverk 1, 2 och 3 till den här väg tabellen. Det finns inget behov av att sprida vägar från förgrenings anslutningar till RT_V2B, medan grenen VNet-trafik går via NVA i VNET4.
  
1. Redigera standard väg tabellen **DefaultRouteTable**.

   Alla VPN-, ExpressRoute-och användares VPN-anslutningar är kopplade till standard väg tabellen. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

   * **Vägar:** Lägg till en sammanställd statisk väg post för virtuella nätverk 1, 2 och 3 (10.1.0.0/16 i **bild 2**) med nästa hopp som VNet 4-anslutningen. Du måste också konfigurera en statisk väg i VNET 4-anslutning för VNET 1, 2 och 3 aggregerade prefix och ange nästa hopp som ska vara den speciella IP-adressen för NVA i VNET 4.

   * **Association:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala gren anslutningar är kopplade till *defaultroutetable*.

   * **Spridningen från:** Se till att alternativet för grenar (VPN/ER/P2S) är markerat, och se till att lokala anslutningar sprider vägar till *defaultroutetable*.

**Bild 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Bild 2":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
