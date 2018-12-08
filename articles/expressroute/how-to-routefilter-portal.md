---
title: 'Konfigurera routningsfilter för Microsoft-peering: Azure ExpressRoute - portalen | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar routningsfilter för Microsoft-peering med hjälp av Azure portal.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104213"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurera routningsfilter för Microsoft-peering: Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera flödesfilter för ExpressRoute-kretsar.

Dynamics 365-tjänster, och Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag och Azure-tjänster som lagring och SQL DB är tillgängliga via Microsoft-peering. När Microsoft-peering har konfigurerats i en ExpressRoute-krets, visas alla prefix som är relaterade till dessa tjänster i BGP-sessioner upprättas. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över BGP community-värden och de tjänster som de mappas till finns i [BGP-communities](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster har ett stort antal prefix annonseras via BGP. Detta ökar avsevärt storleken på routningstabeller som underhålls av routrar i nätverket. Om du planerar att använda en underuppsättning av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

- Filtrera bort oönskade prefix genom att tillämpa flödesfilter på BGP-communities. Detta är ett vanligt nätverk förfarande och används ofta i många nätverk.

- Definiera vägfilter och tillämpa dem på din ExpressRoute-krets. Ett flödesfilter är en ny resurs där du kan välja i listan över tjänster som du tänker använda via Microsoft-peering. ExpressRoute-routrar Skicka endast lista över prefix som hör till de tjänster som identifierats i flödesfiltret.

### <a name="about"></a>Om flödesfilter

Om Microsoft-peering är konfigurerat på ExpressRoute-kretsen, upprätta ett par med BGP-sessioner med edge-routrar (själv eller din anslutningsleverantör) i Microsoft edge-routrar. Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i princip en lista över alla tillåtna community-värden för BGP. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna kopplar du flödesfilter med Office 365-tjänster på dem. måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute, misslyckas åtgärden kopplar du flödesfilter. Läs mer om auktoriseringsprocessen [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Anslutningen till Dynamics 365-tjänster kräver inte någon tillstånd.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen.
> 
> 

### <a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering, måste du utföra följande konfigurationssteg:

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerade. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.
  - [Skapa Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md) om du hanterar BGP-sessionen direkt. Eller låt anslutningsleverantören etablera Microsoft-peering för din krets.

-  Du måste skapa och konfigurera ett flödesfilter.
    - Identifiera tjänsterna du med att använda via Microsoft-peering
    - Identifiera listan över BGP community-värden som är associerade med tjänsterna
    - Skapa en regel för att tillåta Prefixlistan matchande värden för BGP-community

-  Du måste koppla flödesfiltret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurationen måste du kontrollera att du uppfyller följande kriterier:

 - Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.

 - Du måste ha en aktiv Microsoft-peering. Följ anvisningarna i [skapa och ändra peering-konfigurationen](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Steg 1: Hämta en lista över prefix och BGP community-värden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över BGP community-värden

BGP community-värden som är associerade med tjänster som är tillgängliga via Microsoft-peering är tillgänglig i den [ExpressRoute-routningskrav](expressroute-routing.md) sidan.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Skapa en lista över de värden som du vill använda

Se en lista över BGP community-värden som du vill använda i flödesfiltret. Exempelvis är 12076:5040 i BGP-community värde för Dynamics 365-tjänster.

## <a name="filter"></a>Steg 2: Skapa ett flödesfilter och en regel för filter

Ett flödesfilter kan ha endast en regel och regeln måste vara av typen 'Tillåt'. Den här regeln kan ha en lista över BGP community-värden som är associerade med den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett flödesfilter
Du kan skapa ett flödesfilter genom att välja alternativet för att skapa en ny resurs. Klicka på **skapa en resurs** > **nätverk** > **RouteFilter**, enligt följande bild:

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Flödesfiltret måste du placera i en resursgrupp. 

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Skapa en regel för filter

Du kan lägga till och uppdatera regler genom att välja fliken hantera regel för route-filter.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Du kan välja de tjänster du vill ansluta till från nedrullningsbara listan och spara regeln när du är klar.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Steg 3: Koppla flödesfiltret till en ExpressRoute-krets

Du kan koppla flödesfiltret till en krets genom att välja knappen ”Lägg till krets” och välja ExpressRoute-kretsen från nedrullningsbara listan.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Om anslutningsprovidern konfigurerar peering för din ExpressRoute-krets uppdatering kretsen från bladet ExpressRoute-kretsen innan du väljer knappen ”Lägg till krets”.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Vanliga åtgärder

### <a name="getproperties"></a>Att hämta egenskaperna för ett flödesfilter

Du kan visa egenskaperna för ett flödesfilter när du öppnar resursen i portalen.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Att uppdatera egenskaperna för ett flödesfilter

Du kan uppdatera listan över värden för BGP-community är ansluten till en krets genom att välja knappen ”Hantera regeln”.


![Skapa ett flödesfilter](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Att koppla från ett flödesfilter från en ExpressRoute-krets

Om du vill koppla bort en krets från flödesfiltret, högerklicka på kretsen och klicka på ”Ta bort koppling”.

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Att ta bort ett flödesfilter

Du kan ta bort ett flödesfilter genom att välja knappen Ta bort. 

![Skapa ett flödesfilter](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).