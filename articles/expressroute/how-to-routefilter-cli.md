---
title: 'ExpressRoute: väg filter-Microsoft-peering: Azure CLI'
description: Den här artikeln beskriver hur du konfigurerar väg filter för Microsoft-peering med hjälp av Azure CLI
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74033418"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurera väg filter för Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera väg filter för ExpressRoute-kretsar.

Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag är tillgängliga via Microsoft-peering. När Microsoft-peering har kon figurer ATS i en ExpressRoute-krets annonseras alla prefix som är relaterade till dessa tjänster via de BGP-sessioner som etableras. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över värdena för BGP-communityn och de tjänster som de mappar till finns i [BGP-communities](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster annonseras ett stort antal prefix via BGP. Detta ökar markant storleken på de väg tabeller som hanteras av routrarna i nätverket. Om du planerar att endast använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att använda väg filter på BGP-communities. Detta är en vanlig nätverks praxis och används ofta i många nätverk.

* Definiera väg filter och Använd dem i ExpressRoute-kretsen. Ett flödes filter är en ny resurs där du kan välja listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara listan över prefix som hör till de tjänster som identifieras i flödes filtret.

### <a name="about-route-filters"></a><a name="about"></a>Om flödes filter

När Microsoft-peering har kon figurer ATS på din ExpressRoute-krets, upprättar Microsoft Edge-routrarna ett par med BGP-sessioner med gräns routrarna (dina eller din anslutnings leverantör). Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i princip en lista över alla tillåtna community-värden för BGP. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna koppla väg filter med Office 365-tjänster på dem måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute Miss lyckas åtgärden att bifoga väg filter. Mer information om auktoriseringsprocessen finns i [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurations steg:

* Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerad. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  * [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och låt den krets som aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.
  * [Skapa Microsoft-peering](howto-routing-cli.md) om du hanterar BGP-sessionen direkt. Du kan också använda din anslutnings leverantör för att etablera Microsoft-peering för din krets.

* Du måste skapa och konfigurera ett flödes filter.
  * Identifiera de tjänster du använder via Microsoft-peering
  * Identifiera listan över värden för BGP-communityn som är kopplade till tjänsterna
  * Skapa en regel för att tillåta att listan med prefix matchar BGP-communityns värden

* Du måste koppla väg filtret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

* Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.

* Du måste ha en aktiv Microsoft-peering. Följ anvisningarna i [skapa och ändra peering-konfiguration](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

Logga in på ditt Azure-konto för att påbörja konfigurationen. Om du använder "testa det" är du inloggad automatiskt och kan hoppa över inloggnings steget. Använd följande exempel för att ansluta:

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-community

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över värden för BGP-communityn

Använd följande cmdlet för att hämta listan över BGP-gruppvärden som är kopplade till tjänster som är tillgängliga via Microsoft-peering och listan med prefix som är kopplade till dem:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. gör en lista över de värden som du vill använda

Skapa en lista över värden för BGP-grupper som du vill använda i flödes filtret.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Steg 2: skapa ett flödes filter och en filter regel

Ett flödes filter kan bara ha en regel och regeln måste vara av typen Allow. Den här regeln kan ha en lista över värden för BGP-communityn.

### <a name="1-create-a-route-filter"></a>1. skapa ett flödes filter

Börja med att skapa ett flödes filter. Kommandot `az network route-filter create` skapar bara en Route filter-resurs. När du har skapat resursen måste du skapa en regel och koppla den till Route filter-objektet. Kör följande kommando för att skapa en Route filter-resurs:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. skapa en filter regel

Kör följande kommando för att skapa en ny regel:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Steg 3: koppla väg filtret till en ExpressRoute-krets

Kör följande kommando för att koppla väg filtret till ExpressRoute-kretsen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Hämta egenskaperna för ett flödes filter

Använd följande kommando för att hämta egenskaperna för ett flödes filter:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödes filter

Om flödes filtret redan är kopplat till en krets sprider uppdateringar till BGP community-listan automatiskt lämpliga annonserings annonserings ändringar via etablerade BGP-sessioner. Du kan uppdatera listan med BGP-grupper för ditt flödes filter med följande kommando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Koppla bort ett flödes filter från en ExpressRoute-krets

När ett flödes filter kopplas från ExpressRoute-kretsen annonseras inga prefix via BGP-sessionen. Du kan koppla bort ett flödes filter från en ExpressRoute-krets med hjälp av följande kommando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Ta bort ett flödes filter

Du kan bara ta bort ett flödes filter om det inte är kopplat till någon krets. Se till att flödes filtret inte är kopplat till någon krets innan du försöker ta bort det. Du kan ta bort ett flödes filter med följande kommando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Efterföljande moment

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
