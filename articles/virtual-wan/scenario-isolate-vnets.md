---
title: 'Scenario: isolera virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för att isolera virtuella nätverk
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569012"
---
# <a name="scenario-isolating-vnets"></a>Scenario: isolera virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att förhindra att virtuella nätverk kan komma åt andra. Detta kallas att isolera virtuella nätverk. Arbets belastningen i VNet förblir isolerad och kan inte kommunicera med andra virtuella nätverk, som i ett scenario. Virtuella nätverk krävs dock för att komma åt alla grenar (VPN, ER och användarens VPN). I det här scenariot är alla VPN-, ExpressRoute-och användares VPN-anslutningar kopplade till samma och en routningstabell. Alla VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

För att konfigurera det här scenariot vidtar du följande steg för att överväga:

1. Skapa en anpassad routningstabell. I exemplet är routningstabellen **RT_VNet**. Information om hur du skapar en routningstabell finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md). Mer information om vägvals tabeller finns i [om virtuell hubb](about-virtual-hub-routing.md).
2. Konfigurera följande inställningar när du skapar tabellen **RT_VNet** väg:

   * **Association**: Välj den virtuella nätverk som du vill isolera.
   * **Spridning**: Välj alternativet för grenar, vilket innebär att förgreningar (VPN/er/P2s)-anslutningar sprider vägar till den här väg tabellen.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Isolerade virtuella nätverk":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
