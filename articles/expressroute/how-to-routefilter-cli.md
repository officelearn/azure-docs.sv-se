---
title: 'ExpressRoute: Vägfilter – Microsoft peering:Azure CLI'
description: I den hÃ¤r artikeln beskrivs sÃ¤t pÃã¤ konfigurerarr filter fÃ¤ger fÃ¤nderning med Azure CLI
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033418"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurera flödesfilter för Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera flödesfilter för ExpressRoute-kretsar.

Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag kan nås via Microsoft-peering. När Microsoft-peering konfigureras i en ExpressRoute-krets annonseras alla prefix som är relaterade till dessa tjänster via de BGP-sessioner som har upprättats. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över BGP-communityns värden och de tjänster de mappar till finns i [BGP-grupper](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster annonseras ett stort antal prefix via BGP. Detta ökar avsevärt storleken på de vägtabeller som underhålls av routrar i nätverket. Om du planerar att bara använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina flödestabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att använda flödesfilter på BGP-grupper. Detta är en standard nätverkspraxis och används ofta inom många nätverk.

* Definiera flödesfilter och tillämpa dem på din ExpressRoute-krets. Ett flödesfilter är en ny resurs som gör att du kan välja en lista över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara en lista över prefix som tillhör de tjänster som identifieras i flödesfiltret.

### <a name="about-route-filters"></a><a name="about"></a>Om flödesfilter

När Microsoft-peering är konfigurerat på din ExpressRoute-krets upprättar Microsofts kantroutrar ett par BGP-sessioner med kantroutrar (din eller anslutningsleverantörens). Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i princip en lista över alla tillåtna community-värden för BGP. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna koppla flödesfilter med Office 365-tjänster på dem måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute misslyckas åtgärden för att koppla flödesfilter. Mer information om auktoriseringsprocessen finns i [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla tjänstprefix annonserade via Microsoft-peering, även om vägfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade den 1 augusti 2017 eller senare kommer inte att ha några prefix som annonseras förrän ett flödesfilter är kopplat till kretsen.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurationssteg:

* Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerat. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  * [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av din anslutningsleverantör innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.
  * [Skapa Microsoft-peering](howto-routing-cli.md) om du hanterar BGP-sessionen direkt. Du kan också låta anslutningsleverantören etablera Microsoft-peering för din krets.

* Du måste skapa och konfigurera ett flödesfilter.
  * Identifiera de tjänster du använder via Microsoft-peering
  * Identifiera listan över BGP-communityvärden som är associerade med tjänsterna
  * Skapa en regel som tillåter prefixlistan som matchar BGP-communityvärdena

* Du måste koppla flödesfiltret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

* Granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.

* Du måste ha en aktiv Microsoft-peering. Följa instruktioner på [Skapa och ändra peering-konfiguration](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

Logga in på ditt Azure-konto för att starta konfigurationen. Om du använder "Prova", är du inloggad automatiskt och kan hoppa över inloggningssteget. Använd följande exempel för att hjälpa dig att ansluta:

```azurecli
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Steg 1: Få en lista över prefix och BGP-communityvärden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Få en lista över BGP community värden

Använd följande cmdlet för att få en lista över BGP-communityvärden som är associerade med tjänster som är tillgängliga via Microsoft-peering och listan över prefix som är associerade med dem:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Gör en lista över de värden som du vill använda

Gör en lista över BGP-communityvärden som du vill använda i flödesfiltret.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Steg 2: Skapa ett flödesfilter och en filterregel

Ett flödesfilter kan bara ha en regel och regeln måste vara av typen "Tillåt". Den här regeln kan ha en lista över BGP-communityvärden som är associerade med den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett flödesfilter

Skapa först flödesfiltret. Kommandot `az network route-filter create` skapar bara en flödesfilterresurs. När du har skapat resursen måste du sedan skapa en regel och koppla den till flödesfilterobjektet. Kör följande kommando för att skapa en flödesfilterresurs:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Skapa en filterregel

Kör följande kommando för att skapa en ny regel:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Steg 3: Fäst ruttfiltret på en ExpressRoute-krets

Kör följande kommando för att koppla vägfiltret till ExpressRoute-kretsen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Så här hämtar du egenskaperna för ett flödesfilter

Om du vill hämta egenskaperna för ett flödesfilter använder du följande kommando:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödesfilter

Om flödesfiltret redan är kopplat till en krets sprider uppdateringar till BGP-communitylistan automatiskt lämpliga prefixannonsändringar via de etablerade BGP-sessionerna. Du kan uppdatera BGP-communitylistan för ditt flödesfilter med följande kommando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Så här kopplar du bort ett flödesfilter från en ExpressRoute-krets

När ett flödesfilter har kopplats från ExpressRoute-kretsen annonseras inga prefix via BGP-sessionen. Du kan koppla bort ett flödesfilter från en ExpressRoute-krets med följande kommando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Så här tar du bort ett flödesfilter

Du kan bara ta bort ett flödesfilter om det inte är kopplat till någon krets. Kontrollera att flödesfiltret inte är anslutet till någon krets innan du försöker ta bort det. Du kan ta bort ett flödesfilter med följande kommando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Efterföljande moment

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
