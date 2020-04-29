---
title: 'ExpressRoute: väg filter-Microsoft-peering: Azure Portal'
description: Den här artikeln beskriver hur du konfigurerar väg filter för Microsoft-peering med hjälp av Azure Portal.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f2be9b4e7152c61885b1a41e94ebd328059d437b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618552"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurera väg filter för Microsoft-peering: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera väg filter för ExpressRoute-kretsar.

Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag, och Azure-tjänster som lagring och SQL DB, är tillgängliga via Microsoft-peering. När Microsoft-peering har kon figurer ATS i en ExpressRoute-krets annonseras alla prefix som är relaterade till dessa tjänster via de BGP-sessioner som etableras. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över värdena för BGP-communityn och de tjänster som de mappar till finns i [BGP-communities](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster annonseras ett stort antal prefix via BGP. Detta ökar markant storleken på de väg tabeller som hanteras av routrarna i nätverket. Om du planerar att endast använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

- Filtrera bort oönskade prefix genom att använda väg filter på BGP-communities. Detta är en vanlig nätverks praxis och används ofta i många nätverk.

- Definiera väg filter och Använd dem i ExpressRoute-kretsen. Ett flödes filter är en ny resurs där du kan välja listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara listan över prefix som hör till de tjänster som identifieras i flödes filtret.

### <a name="about-route-filters"></a><a name="about"></a>Om flödes filter

När Microsoft-peering har kon figurer ATS på din ExpressRoute-krets, upprättar Microsoft Edge-routrarna ett par med BGP-sessioner med gräns routrarna (dina eller din anslutnings leverantör). Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i grunden en lista över alla värden för BGP-communityn som du vill tillåta. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna koppla väg filter med Office 365-tjänster på dem måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute Miss lyckas åtgärden att bifoga väg filter. Mer information om auktoriseringsprocessen finns i [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurations steg:

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerad. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och låt den krets som aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.
  - [Skapa Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md) om du hanterar BGP-sessionen direkt. Du kan också använda din anslutnings leverantör för att etablera Microsoft-peering för din krets.

-  Du måste skapa och konfigurera ett flödes filter.
    - Identifiera de tjänster du använder via Microsoft-peering
    - Identifiera listan över värden för BGP-communityn som är kopplade till tjänsterna
    - Skapa en regel för att tillåta att listan med prefix matchar BGP-communityns värden

-  Du måste koppla väg filtret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar konfigurationen kontrollerar du att du uppfyller följande kriterier:

 - Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.

 - Du måste ha en aktiv Microsoft-peering. Följ anvisningarna i [skapa och ändra peering-konfiguration](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-community

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över värden för BGP-communityn

BGP community-värden som är kopplade till tjänster som är tillgängliga via Microsoft-peering finns på sidan [ExpressRoute-krav för routning](expressroute-routing.md) .

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. gör en lista över de värden som du vill använda

Skapa en lista över [värden för BGP-grupper](expressroute-routing.md#bgp) som du vill använda i flödes filtret. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Steg 2: skapa ett flödes filter och en filter regel

Ett flödes filter kan bara ha en regel och regeln måste vara av typen Allow. Den här regeln kan ha en lista över värden för BGP-communityn.

### <a name="1-create-a-route-filter"></a>1. skapa ett flödes filter
Du kan skapa ett flödes filter genom att välja alternativet för att skapa en ny resurs. Klicka på **skapa en resurs** > **nätverks** > **RouteFilter**, som du ser i följande bild:

![Skapa ett flödes filter](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Du måste placera flödes filtret i en resurs grupp. 

![Skapa ett flödes filter](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. skapa en filter regel

Du kan lägga till och uppdatera regler genom att välja fliken Hantera regel för ditt flödes filter.

![Skapa ett flödes filter](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Du kan välja de tjänster som du vill ansluta till från List rutan och spara regeln när du är färdig.

![Skapa ett flödes filter](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Steg 3: koppla väg filtret till en ExpressRoute-krets

Du kan koppla flödes filtret till en krets genom att välja knappen Lägg till krets och välja ExpressRoute-kretsen i list rutan.

![Skapa ett flödes filter](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Om anslutnings leverantören konfigurerar peering för ExpressRoute-kretsen uppdaterar du kretsen från ExpressRoute-krets-bladet innan du väljer knappen Lägg till krets.

![Skapa ett flödes filter](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Hämta egenskaperna för ett flödes filter

Du kan visa egenskaperna för ett flödes filter när du öppnar resursen i portalen.

![Skapa ett flödes filter](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödes filter

Du kan uppdatera listan med BGP community-värden som är kopplade till en krets genom att välja knappen "hantera regel".


![Skapa ett flödes filter](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Skapa ett flödes filter](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Koppla bort ett flödes filter från en ExpressRoute-krets

Om du vill koppla bort en krets från väg filtret högerklickar du på kretsen och klickar på "ta bort koppling".

![Skapa ett flödes filter](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Ta bort ett flödes filter

Du kan ta bort ett flödes filter genom att klicka på knappen Ta bort. 

![Skapa ett flödes filter](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Nästa steg

* Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

* Information om exempel på routerkonfigurationen finns i [konfigurations exempel för routern för att konfigurera och hantera routning](expressroute-config-samples-routing.md). 
