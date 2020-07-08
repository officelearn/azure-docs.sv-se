---
title: 'Scenario: dirigera trafik via en NVA'
titleSuffix: Azure Virtual WAN
description: Dirigera trafik via NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0716ca8f0457ca801098c97dd7a5e68751822d4d
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848094"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: dirigera trafik via en NVA

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här NVA-scenariot är målet att dirigera trafik via en NVA (virtuell nätverks installation) för gren till VNet och VNet till gren. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenariots arkitektur

I **bild 1**finns det två hubbar. **Hubb 1** och **hubb 2**.

* **Hubb 1** och **Hub 2** är direkt anslutna till NVA virtuella nätverk **VNet 2** och **VNet 4**.

* **VNet 5** och **VNet 6** är peer-datorer med **VNet 2**.

* **VNet 7** och **VNet 8** är peer-datorer med **VNet 4**.

* **Virtuella nätverk 5, 6, 7, 8** är indirekta ekrar, inte direkt anslutna till en virtuell hubb.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Bild 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

Om du vill konfigurera routning via NVA följer du stegen nedan:

1. Identifiera NVA eker VNet-anslutningen. I **bild 1**är det **VNet 2-anslutning (Eastusconn)** och **VNet 4-anslutning (weconn)**.

   Se till att UDR har kon figurer ATS:
   * Från VNET 5 och 6 till VNET 2 NVA IP
   * Från VNET 7 och 8 till VNET 4 NVA IP 
   
   Du behöver inte ansluta VNET 5, 6, 7, 8 till de virtuella hubbarna direkt. Se till att NSG: er i virtuella nätverk 5, 6, 7, 8 tillåter trafik för grenen (VPN/ER/P2S) eller virtuella nätverk som är anslutna till sin fjärran sluten virtuella nätverk. VNET 5, 6 måste till exempel säkerställa att NSG: er tillåter trafik för lokala adressprefix och virtuella nätverk 7, 8 som är anslutna till fjärrhubben 2. 

2. Lägg till en sammanställd statisk väg post för virtuella nätverk 2, 5, 6 till hubb 1 standard väg tabell. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exempel":::

3. Konfigurera en statisk väg för virtuella nätverk 5, 6 i VNET 2: s virtuella nätverks anslutning. Information om hur du konfigurerar konfigurering av routning för en virtuell nätverks anslutning finns i [routning av virtuell hubb](how-to-virtual-hub-routing.md#routing-configuration).

4. Lägg till en sammanställd statisk väg post för virtuella nätverk 4, 7, 8 till hubb 1 standard väg tabell.

5. Upprepa steg 2, 3 och 4 för standard väg tabellen i hubb 2.

Detta leder till att konfigurations konfigurationen ändras så som visas i figuren nedan

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Resultat":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
