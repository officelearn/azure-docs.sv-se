---
title: 'ExpressRoute: väg filter-Microsoft-peering: Azure PowerShell'
description: Den här artikeln beskriver hur du konfigurerar väg filter för Microsoft-peering med hjälp av PowerShell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f5a294a051350c4b08b34356abcd883b7580164e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729310"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurera väg filter för Microsoft-peering: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera väg filter för ExpressRoute-kretsar.

Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag, och offentliga Azure-tjänster, t. ex. lagring och SQL DB, är tillgängliga via Microsoft-peering. Offentliga Azure-tjänster kan väljas per region och kan inte definieras per offentlig tjänst.

När Microsoft-peering har kon figurer ATS på en ExpressRoute-krets och ett flödes filter är kopplat annonseras alla prefix som valts för dessa tjänster via de BGP-sessioner som etableras. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över värdena för BGP-communityn och de tjänster som de mappar till finns i [BGP-communities](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster annonseras ett stort antal prefix via BGP. Detta ökar markant storleken på de väg tabeller som hanteras av routrarna i nätverket. Om du planerar att endast använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

- Filtrera bort oönskade prefix genom att använda väg filter på BGP-communities. Detta är en vanlig nätverks praxis och används ofta i många nätverk.

- Definiera väg filter och Använd dem i ExpressRoute-kretsen. Ett flödes filter är en ny resurs där du kan välja listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara listan över prefix som hör till de tjänster som identifieras i flödes filtret.

### <a name="about-route-filters"></a><a name="about"></a>Om flödes filter

När Microsoft-peering har kon figurer ATS på din ExpressRoute-krets upprättar Microsoft Network Edge-routrarna ett par med BGP-sessioner med Edge-routrarna (dina eller anslutnings leverantörens). Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i stort sett en tillåten lista över alla värden för BGP-communityn. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna koppla väg filter med Office 365-tjänster på dem måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute Miss lyckas åtgärden att bifoga väg filter. Mer information om auktoriseringsprocessen finns i [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurations steg:

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerad. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och låt den krets som aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.
  - [Skapa Microsoft-peering](expressroute-circuit-peerings.md) om du hanterar BGP-sessionen direkt. Du kan också använda din anslutnings leverantör för att etablera Microsoft-peering för din krets.

-  Du måste skapa och konfigurera ett flödes filter.
    - Identifiera de tjänster du använder via Microsoft-peering
    - Identifiera listan över värden för BGP-communityn som är kopplade till tjänsterna
    - Skapa en regel för att tillåta att listan med prefix matchar BGP-communityns värden

-  Du måste koppla väg filtret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar konfigurationen kontrollerar du att du uppfyller följande kriterier:

 - Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.

 - Du måste ha en aktiv Microsoft-peering. Följ anvisningarna i artikeln [skapa och ändra peering-konfiguration](expressroute-circuit-peerings.md) .


### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Innan du påbörjar konfigurationen måste du logga in på ditt Azure-konto. Den här cmdleten uppmanar dig att ange inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.

Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att få hjälp att ansluta. Om du använder Azure Cloud Shell behöver du inte köra den här cmdleten, eftersom du loggas in automatiskt.

```azurepowershell
Connect-AzAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```azurepowershell-interactive
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-community

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över värden för BGP-communityn

Använd följande cmdlet för att hämta listan över BGP-gruppvärden som är kopplade till tjänster som är tillgängliga via Microsoft-peering och listan med prefix som är kopplade till dem:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. gör en lista över de värden som du vill använda

Skapa en lista över värden för BGP-grupper som du vill använda i flödes filtret.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Steg 2: skapa ett flödes filter och en filter regel

Ett flödes filter kan bara ha en regel och regeln måste vara av typen Allow. Den här regeln kan ha en lista över värden för BGP-communityn.

### <a name="1-create-a-route-filter"></a>1. skapa ett flödes filter

Börja med att skapa ett flödes filter. Kommandot New-AzRouteFilter skapar bara en Route filter-resurs. När du har skapat resursen måste du skapa en regel och koppla den till Route filter-objektet. Kör följande kommando för att skapa en Route filter-resurs:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. skapa en filter regel

Du kan ange en uppsättning BGP-communities som en kommaavgränsad lista, som du ser i exemplet. Kör följande kommando för att skapa en ny regel:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Lägg till regeln i flödes filtret

Kör följande kommando för att lägga till filter regeln i flödes filtret:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Steg 3: koppla väg filtret till en ExpressRoute-krets

Kör följande kommando för att koppla väg filtret till ExpressRoute-kretsen, förutsatt att du bara har Microsoft-peering:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Hämta egenskaperna för ett flödes filter

Använd följande steg för att hämta egenskaperna för ett flödes filter:

1. Kör följande kommando för att hämta Route filter-resursen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Hämta flödes filter reglerna för Route-filter-resursen genom att köra följande kommando:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödes filter

Om flödes filtret redan är kopplat till en krets sprider uppdateringar till BGP community-listan automatiskt lämpliga annonserings annonserings ändringar via etablerade BGP-sessioner. Du kan uppdatera listan med BGP-grupper för ditt flödes filter med följande kommando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Koppla bort ett flödes filter från en ExpressRoute-krets

När ett flödes filter kopplas från ExpressRoute-kretsen annonseras inga prefix via BGP-sessionen. Du kan koppla bort ett flödes filter från en ExpressRoute-krets med hjälp av följande kommando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Ta bort ett flödes filter

Du kan bara ta bort ett flödes filter om det inte är kopplat till någon krets. Se till att flödes filtret inte är kopplat till någon krets innan du försöker ta bort det. Du kan ta bort ett flödes filter med följande kommando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Efterföljande moment

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
