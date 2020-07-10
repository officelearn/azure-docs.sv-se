---
title: Om routning av virtuell hubb
titleSuffix: Azure Virtual WAN
description: I den här artikeln beskrivs routning av virtuell hubb
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 368440976558730bd7bb9600a0bf45c56d1ee300
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147386"
---
# <a name="about-virtual-hub-routing"></a>Om routning av virtuell hubb

Routningsfunktioner i en virtuell hubb tillhandahålls av en router som hanterar all routning mellan gateways med hjälp av Border Gateway Protocol (BGP). En virtuell hubb kan innehålla flera gatewayer som en plats-till-plats-VPN-gateway, ExpressRoute Gateway, punkt-till-plats-Gateway, Azure-brandvägg. Den här routern tillhandahåller även överförings anslutning mellan virtuella nätverk som ansluter till en virtuell hubb och har stöd för upp till ett sammanställt data flöde på 50 Gbit/s. Dessa routningsfunktioner gäller för virtuella standard WAN-kunder.

Information om hur du konfigurerar routning finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Routa koncept

I följande avsnitt beskrivs viktiga begrepp i routning av virtuell hubb.

> [!NOTE]
> Några av dessa nya begrepp för NAV Route-tabellen, associationen, spridningen och statiska vägar i en virtuell nätverks anslutning kan fortfarande återställas och förväntas slutföras i veckan för aug tredje.
>

### <a name="hub-route-table"></a><a name="hub-route"></a>Hubb väg tabell

En väg tabell för virtuell hubb kan innehålla en eller flera vägar. En väg innehåller dess namn, en etikett, en måltyp, en lista med mål prefix och nästa hopp information för ett paket som ska dirigeras. En **anslutning** har vanligt vis en routningslänk som associeras eller sprids till en routningstabell

### <a name="connection"></a><a name="connection"></a>Anslutning

Anslutningar är Resource Manager-resurser som har en konfiguration för routning. De fyra typerna av anslutningar är:

* **VPN-anslutning**: ansluter en VPN-plats till en virtuell HUBBS-VPN-gateway.
* **ExpressRoute anslutning**: ansluter en ExpressRoute-krets till en virtuell hubb ExpressRoute-Gateway.
* **P2s konfigurations anslutning**: ansluter en ANVÄNDAREs VPN-konfiguration (punkt-till-plats) till en virtuell hubb användare VPN-gateway (punkt-till-plats).
* **Hubb anslutning till virtuellt nätverk**: ansluter virtuella nätverk till en virtuell hubb.

Du kan ställa in konfigureringen av routning för en virtuell nätverks anslutning under installationen. Som standard är alla anslutningar kopplade till och sprider till standard väg tabellen.

### <a name="association"></a><a name="association"></a>Föreningar

Varje anslutning är kopplad till en routningstabell. Om du kopplar en anslutning till en routningstabell kan trafiken skickas till målet som anges som vägar i routningstabellen. I Dirigerings konfigurationen för anslutningen visas den tillhör ande routningstabellen.  Flera anslutningar kan kopplas till samma routningstabell. Alla VPN-, ExpressRoute-och användares VPN-anslutningar är kopplade till samma (standard) väg tabell.

Som standard är alla anslutningar kopplade till en **standard väg tabell** i en virtuell hubb. Varje virtuell hubb har sin egen standard väg tabell som kan redige ras för att lägga till en eller flera statiska vägar. Vägar som har lagts till statiskt prioriteras framför dynamiskt inlärda vägar för samma prefix.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Föreningar":::

### <a name="propagation"></a><a name="propagation"></a>Spridning

Anslutningar sprider vägar dynamiskt till en routningstabell. Med en VPN-anslutning, ExpressRoute-anslutning eller P2S-konfiguration, sprids vägar från den virtuella hubben till den lokala routern med hjälp av BGP. Vägar kan spridas till en eller flera routningstabeller.

Det finns också en **tabell med ingen väg** för varje virtuell hubb. Spridningen till tabellen none Route innebär att det inte krävs några vägar för att spridas från anslutningen. VPN-, ExpressRoute-och användares VPN-anslutningar sprider vägar till samma uppsättning routningstabeller.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Spridning":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Konfigurera statiska vägar i en virtuell nätverks anslutning

Genom att konfigurera statiska vägar får du en mekanism för att styra trafik via en nästa hopp-IP, som kan vara en virtuell nätverks installation (NVA) etablerad i ett eker VNet som är kopplat till en virtuell hubb. Den statiska vägen består av ett väg namn, en lista över mål-prefix och en nästa hopp-IP.

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Dirigera tabeller i grundläggande och standard-virtuella WAN-nätverk före funktions uppsättningen för Association och spridning

Routningstabeller har nu funktioner för Association och spridning. En fördefinierad routningstabell är en routningstabell som inte har dessa funktioner. Om du har befintliga vägar i hubben Routning och vill använda de nya funktionerna bör du tänka på följande:

* **Virtuella standard WAN-kunder med redan befintliga vägar i virtuell hubb**:

Om du vill använda nya funktioner i en routningstabell, vänta till en vecka om den tredje för distributionen i Azure ska slutföras. Om du har befintliga vägar i avsnittet routning för hubben i Azure Portal måste du först ta bort dem och sedan försöka skapa nya routningstabeller (finns i avsnittet Route tables för hubben i Azure Portal)

* **Grundläggande virtuella WAN-kunder med befintliga vägar i virtuell hubb**: om du vill använda de nya funktionerna i en routningstabell väntar du tills veckan på den tredje för distributionen i Azure ska slutföras. Om du har redan befintliga vägar i avsnittet routning för hubben i Azure Portal måste du först ta bort dem och sedan **Uppgradera** ditt grundläggande virtuella WAN-nätverk till standard. Se [uppgradera ett virtuellt WAN från Basic till standard](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar routning finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md).

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
