---
title: 'ExpressRoute: Vägfilter- Microsoft-peering:Azure PowerShell'
description: I den hÃ¤r artikeln beskrivs sÃ¤t pÃã¤ konfigurerarr filter fÃ¤r Microsoft Peering med PowerShell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618604"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurera flödesfilter för Microsoft-peering: PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera flödesfilter för ExpressRoute-kretsar.

Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag och offentliga Azure-tjänster, till exempel lagring och SQL DB, är tillgängliga via Microsoft-peering. Offentliga Azure-tjänster kan väljas per region och kan inte definieras per offentlig tjänst.

När Microsoft-peering konfigureras på en ExpressRoute-krets och ett flödesfilter är anslutet annonseras alla prefix som har valts för dessa tjänster via de BGP-sessioner som har upprättats. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över BGP-communityns värden och de tjänster de mappar till finns i [BGP-grupper](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster annonseras ett stort antal prefix via BGP. Detta ökar avsevärt storleken på de vägtabeller som underhålls av routrar i nätverket. Om du planerar att bara använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina flödestabeller på två sätt. Du kan:

- Filtrera bort oönskade prefix genom att använda flödesfilter på BGP-grupper. Detta är en standard nätverkspraxis och används ofta inom många nätverk.

- Definiera flödesfilter och tillämpa dem på din ExpressRoute-krets. Ett flödesfilter är en ny resurs som gör att du kan välja en lista över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara en lista över prefix som tillhör de tjänster som identifieras i flödesfiltret.

### <a name="about-route-filters"></a><a name="about"></a>Om flödesfilter

När Microsoft-peering är konfigurerat på din ExpressRoute-krets upprättar Microsofts nätverkskantroutrar ett par BGP-sessioner med kantroutrar (din eller anslutningsleverantörens). Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i huvudsak en tillåt lista över alla BGP community värden. När en flödesfilterresurs har definierats och kopplats till en ExpressRoute-krets, annonseras alla prefix som mappar till community-värden för BGP till ditt nätverk.

Om du vill kunna koppla flödesfilter med Office 365-tjänster på dem måste du ha behörighet att använda Office 365-tjänster via ExpressRoute. Om du inte har behörighet att använda Office 365-tjänster via ExpressRoute misslyckas åtgärden för att koppla flödesfilter. Mer information om auktoriseringsprocessen finns i [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla tjänstprefix annonserade via Microsoft-peering, även om vägfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade den 1 augusti 2017 eller senare kommer inte att ha några prefix som annonseras förrän ett flödesfilter är kopplat till kretsen.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurationssteg:

- Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerat. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av din anslutningsleverantör innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.
  - [Skapa Microsoft-peering](expressroute-circuit-peerings.md) om du hanterar BGP-sessionen direkt. Du kan också låta anslutningsleverantören etablera Microsoft-peering för din krets.

-  Du måste skapa och konfigurera ett flödesfilter.
    - Identifiera de tjänster du använder via Microsoft-peering
    - Identifiera listan över BGP-communityvärden som är associerade med tjänsterna
    - Skapa en regel som tillåter prefixlistan som matchar BGP-communityvärdena

-  Du måste koppla flödesfiltret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera, se till att du uppfyller följande kriterier:

 - Granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.

 - Du måste ha en aktiv Microsoft-peering. Följ instruktionerna i artikeln [Skapa och ändra peering-konfiguration.](expressroute-circuit-peerings.md)


### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Innan du påbörjar konfigurationen måste du logga in på ditt Azure-konto. Den här cmdleten uppmanar dig att ange inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.

Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att hjälpa dig att ansluta. Om du använder Azure Cloud Shell behöver du inte köra den här cmdleten, eftersom du automatiskt loggas in.

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Steg 1: Få en lista över prefix och BGP-communityvärden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Få en lista över BGP community värden

Använd följande cmdlet för att få en lista över BGP-communityvärden som är associerade med tjänster som är tillgängliga via Microsoft-peering och listan över prefix som är associerade med dem:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Gör en lista över de värden som du vill använda

Gör en lista över BGP-communityvärden som du vill använda i flödesfiltret.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Steg 2: Skapa ett flödesfilter och en filterregel

Ett flödesfilter kan bara ha en regel och regeln måste vara av typen "Tillåt". Den här regeln kan ha en lista över BGP-communityvärden som är associerade med den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett flödesfilter

Skapa först flödesfiltret. Kommandot "New-AzRouteFilter" skapar bara en flödesfilterresurs. När du har skapat resursen måste du sedan skapa en regel och koppla den till flödesfilterobjektet. Kör följande kommando för att skapa en flödesfilterresurs:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Skapa en filterregel

Du kan ange en uppsättning BGP-grupper som en kommaavgränsad lista, som visas i exemplet. Kör följande kommando för att skapa en ny regel:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Lägg till regeln i flödesfiltret

Kör följande kommando om du vill lägga till filterregeln i flödesfiltret:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Steg 3: Fäst ruttfiltret på en ExpressRoute-krets

Kör följande kommando för att koppla vägfiltret till ExpressRoute-kretsen, förutsatt att du bara har Microsoft-peering:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Så här hämtar du egenskaperna för ett flödesfilter

Så här hämtar du egenskaperna för ett flödesfilter:

1. Kör följande kommando för att hämta flödesfilterresursen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Hämta flödesfilterreglerna för flödesfilterresursen genom att köra följande kommando:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödesfilter

Om flödesfiltret redan är kopplat till en krets sprider uppdateringar till BGP-communitylistan automatiskt lämpliga prefixannonsändringar via de etablerade BGP-sessionerna. Du kan uppdatera BGP-communitylistan för ditt flödesfilter med följande kommando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Så här kopplar du bort ett flödesfilter från en ExpressRoute-krets

När ett flödesfilter har kopplats från ExpressRoute-kretsen annonseras inga prefix via BGP-sessionen. Du kan koppla bort ett flödesfilter från en ExpressRoute-krets med följande kommando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Så här tar du bort ett flödesfilter

Du kan bara ta bort ett flödesfilter om det inte är kopplat till någon krets. Kontrollera att flödesfiltret inte är anslutet till någon krets innan du försöker ta bort det. Du kan ta bort ett flödesfilter med följande kommando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Efterföljande moment

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
