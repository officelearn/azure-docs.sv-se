---
title: 'Konfigurera filter för routning för Azure ExpressRoute Microsoft peering: Portal | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar filter för routning för Microsoft-Peering i Azure Portal
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: ab0cd45334581f6f5c6dd6e86939e018df5de074
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29387735"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurera filter för routning för Microsoft-peering: Azure-portalen
> [!div class="op_single_selector"]
> * [Azure-portalen](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Vägfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälpa dig att konfigurera och hantera filter för routning för ExpressRoute-kretsar.

Dynamics 365-tjänster, och Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag och Azure-tjänster, till exempel lagring och SQL-databas är tillgängliga via Microsoft-peering. När Microsoft-peering konfigureras i en ExpressRoute-krets, visas alla prefix som är relaterade till tjänsterna i BGP-sessioner som upprättas. Ett värde för BGP-gemenskapen är kopplad till varje prefix för att identifiera den tjänst som erbjuds via prefixet. En lista över BGP community-värden och de tjänster som de mappas till finns [BGP communities](expressroute-routing.md#bgp).

Om du behöver ansluta till alla tjänster har ett stort antal prefix annonserats via BGP. Detta ökar avsevärt storleken på vägtabeller upprätthålls av routrar i nätverket. Om du planerar att använda endast en delmängd av tjänster som erbjuds via Microsoft peering kan du minska storleken på din vägtabeller på två sätt. Du kan:

- Filtrera bort oönskade prefix genom att använda filter för routning på BGP-communities. Detta är ett vanligt nätverk förfarande och används ofta i flera nätverk.

- Definiera filter för Routning och koppla dem till ExpressRoute-kretsen. Ett filter för vägen är en ny resurs där du kan välja i listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar bara skicka listan över prefix som hör till de tjänster som identifierats i filtret vägen.

### <a name="about"></a>Om vägen filter

När Microsoft-peering har konfigurerats på ExpressRoute-krets, upprättar Microsoft edge routrar ett par med BGP-sessioner med kant-routrar (ditt eller leverantören connectivity). Inga vägar annonseras till nätverket. Om du vill aktivera vägannonser i nätverket, måste du associera en vägfilter.

En vägfilter kan du identifiera tjänster du vill använda via ExpressRoute-krets Microsoft-peering. Det är i princip en lista för tillåten för alla värden för BGP-communityn. När en resurs för vägen filter är definierad och ansluten till en ExpressRoute-krets, annonseras alla prefix som mappas till värdena för BGP-gemenskapen till nätverket.

Om du vill kunna koppla vägfilter med Office 365-tjänster på dem. måste du ha tillstånd att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute, misslyckas åtgärden att koppla filter för routning. Läs mer om auktoriseringen [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Anslutningen till Dynamics 365-tjänster kräver inte någon tillstånd.

> [!IMPORTANT]
> Microsoft-peering i ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service prefix annonserade via Microsoft peering, även om filter routning inte har definierats. Microsoft-peering i ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett filter för flödet är kopplat till kretsen.
> 
> 

### <a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster med hjälp av Microsoft peering, måste du utföra följande konfigurationssteg:

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft peering etablerade. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha kretsen aktiveras med anslutningsleverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.
  - [Skapa Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md) om du hanterar BGP-sessionen direkt. Har anslutningsleverantören etablera Microsoft-peering för kretsen.

-  Du måste skapa och konfigurera ett filter för vägen.
    - Identifiera tjänsterna du med att använda via Microsoft-peering
    - Identifiera listan över BGP community-värden som är kopplade till tjänsterna
    - Skapa en regel för att tillåta listan över prefix matchande BGP community-värden

-  Du måste koppla filtret vägen till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera måste du kontrollera att du uppfyller följande kriterier:

 - Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.

 - Du måste ha ett aktivt Microsoft-peering. Följ anvisningarna på [skapa och ändra peering konfiguration](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-gemenskapen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över BGP community värden

BGP community-värden som är kopplad till tjänster som är tillgänglig via Microsoft-peering är tillgängliga i den [ExpressRoute routningskrav](expressroute-routing.md) sidan.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Skapa en lista över de värden som du vill använda

Se en lista över BGP community-värden som du vill använda i filtret vägen. Exempelvis är värdet för BGP-community för Dynamics 365-tjänster 12076:5040.

## <a name="filter"></a>Steg 2: Skapa ett flöde filter och en filterregeln

En vägfilter kan ha endast en regel och regeln måste vara av typen ”Tillåt'. Den här regeln kan ha en lista över BGP community-värden som är kopplade till den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett vägfilter
Du kan skapa en vägfilter genom att välja alternativet för att skapa en ny resurs. Klicka på **skapar du en resurs** > **nätverk** > **RouteFilter**, enligt följande bild:

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Du måste placera route-filtret i en resursgrupp. 

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Skapa en regel för filter

Du kan lägga till och uppdatera regler genom att välja fliken hantera regeln för vägfilter.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Du kan välja vilka tjänster som du vill ansluta till från nedrullningsbara listan och spara regeln när du är klar.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>Steg 3: Koppla filtret vägen till en ExpressRoute-krets

Du kan koppla filtret vägen till en krets genom att välja knappen ”Lägg till kretsen” och välja ExpressRoute-kretsen från nedrullningsbara listan.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

Om anslutningen providern konfigurerar peering för din ExpressRoute-kretsen uppdatering krets från bladet ExpressRoute-kretsen innan du klickar på ”Lägg till kretsen”.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Vanliga åtgärder

### <a name="getproperties"></a>Att hämta egenskaperna för en vägfilter

Du kan visa egenskaperna för en vägfilter när du öppnar resursen i portalen.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>Uppdatera egenskaperna för en vägfilter

Du kan uppdatera listan över BGP community värden är ansluten till en krets genom att välja knappen ”Hantera regeln”.


![Skapa ett vägfilter](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>Att ta bort ett filter för vägen från en ExpressRoute-krets

Om du vill koppla en krets från vägen filtret, högerklickar du på kretsen och klicka på ”avassociera”.

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>Ta bort ett vägfilter

Du kan ta bort ett filter för vägen genom att välja knappen Ta bort. 

![Skapa ett vägfilter](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).