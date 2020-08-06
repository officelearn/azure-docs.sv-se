---
title: Övervaka Azure Virtual WAN med Azure Monitor Insights
description: Lär dig mer om övervakning av virtuella WAN-nätverk med hjälp av Azure Monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836079"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor insikter för virtuellt WAN (för hands version)

[Azure Monitor insikter](../azure-monitor/insights/network-insights-overview.md) för virtuellt WAN-nätverk ger användare och operatörer möjlighet att visa status och status för det virtuella WAN-nätverket som presenteras via en automatiskt identifierad Top ologiska karta. Resursens tillstånd och status finns på kartan för att ge dig en Snapshot-vy över den övergripande hälsan för det virtuella WAN-nätverket. Resurs navigeringen är aktive rad på kartan via ett klick till resurs konfigurations sidorna i den virtuella WAN-portalen.

Mått för virtuella WAN-resurs nivåer samlas in och presenteras via en förpackad virtuell WAN-mått arbets bok som visar måtten på ett virtuellt WAN-, Hubbs-, gateway-och anslutnings nivå. Den här artikeln vägleder dig genom stegen för att använda Azure Monitor insikter för virtuellt WAN för att visa din virtuella WAN-topologi och alla mått på en enda plats.

> [!NOTE]
> Meny alternativet insikter i Virtual WAN-portalen håller på att lanseras. Även om insikter-menyn för virtuellt WAN-nätverk distribueras, kan virtuella WAN-topologier och mått arbets böcker nås direkt med Azure Montor för nätverk. Mer information finns i [Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) . 
>

## <a name="before-you-begin"></a>Innan du börjar

Stegen i den här artikeln förutsätter att du redan har distribuerat ett virtuellt WAN med ett eller flera hubbar. Om du vill skapa ett nytt virtuellt WAN-nätverk och en ny hubb använder du stegen i följande artiklar:

* [Skapa ett virtuellt WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Skapa en hubb](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Visa VWAN-topologi

I **Azure Portal->virtuella WAN-nätverket**går du till menyn **övervaka** till vänster och väljer **insikter (för hands version)**. Detta visar **vyn insikter**som visar den virtuella WAN-beroende kartan och hög arbets bok med mått på hög nivå.

**Bild 1: övervaka-insikter-menyn**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="ta" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

I vyn **insikter** kan du Visa de automatiskt identifierade virtuella WAN-resurserna, till exempel hubbar, gatewayer, brand väggar, anslutningar och ekrar virtuella nätverk, NVA i tredje part och grenar i en slutpunkt-till-slutpunkt-virtuell WAN, som visas i **bild 2**.

**Resursens tillstånd** och **status** är färgkodade och finns på resurs ikonerna i kartan. Virtuella WAN-mått på hög nivå, till exempel nav kapaciteter och gateway-användning, visas till höger via en mini-arbets bok.

**Bild 2: vyn insikter**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="ta" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Beroende vy

**Beroende** vyn för virtuella WAN-nätverk gör det lättare att visualisera den sammankopplade vyn över alla virtuella WAN-resurser i en nav-och-eker-arkitektur.

**Bild 3: VWAN beroende vy**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Beroende karta" lightbox="./media/azure-monitor-insights/dependency-map.png":::

I den beroende visnings kartan visas följande resurser som ett anslutet diagram:

* Virtuella WAN-hubbar i olika Azure-regioner.
* Eker-virtuella nätverk som är direkt anslutna till hubben.
* VPN-och ExpressRoute-avdelnings platser och P2S-användare som är anslutna till varje hubb via deras respektive ExpressRoute-, S2S-och P2S-anslutningar och virtuella Nätverksgatewayen.
* Azure-brandväggar (inklusive tredjeparts Firewall-proxyservrar) distribuerade i en hubb (skyddad hubb).
* NVA från tredje part (virtuella nätverks enheter) som distribueras i en eker-virtuella nätverk.

Beroende kartan visar även indirekt anslutna virtuella nätverk (VNet som peer-kopplas med en virtuell WAN-virtuella nätverk).

Beroende kartan möjliggör enkel navigering till konfigurations inställningarna för varje resurs. Du kan till exempel hovra över Hub-resursen för att visa den grundläggande resurs konfigurationen, till exempel Hub-regionen och hubben. Högerklicka för att öppna sidan Azure Portal i nav resursen.

**Bild 4: navigera till resursinformation**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="resursinformation":::

Sök-och filter fältet i beroende vyn ger ett smidigt sätt att söka igenom grafen. Olika filter ger hjälp att begränsa sökningen till en specifik sökväg och tillstånd.

**Bild 5: sökning och filtrering**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Sök och filtrera fält" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Detaljerade mått

Du kan välja **Visa detaljerade mått** för att få åtkomst till sidan detaljerade **mått** . Sidan mått är en instrument panel som är förkonfigurerad med separata flikar och ger användbara insikter om din virtuella WAN-resurs kapacitet, prestanda och användning på den virtuella WAN-nivån, hubbnivå och enskilda anslutningar.

**Bild 6: instrument panel för detaljerade mått**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="detaljerade mått" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Nästa steg

* Mer information om mått i Azure Monitor finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
* En fullständig beskrivning av alla virtuella WAN-mått finns i [virtuella WAN-loggar och mått](logs-metrics.md).
