---
title: 'Konfigurera filter för routning för Azure ExpressRoute Microsoft peering: PowerShell | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar filter för routning för Microsoft-Peering med hjälp av PowerShell
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
ms.openlocfilehash: 6e767166ecf248aa0e7fc16dc21361394e03107d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurera filter för routning för Microsoft-peering: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Vägfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälpa dig att konfigurera och hantera filter för routning för ExpressRoute-kretsar.

Dynamics 365-tjänster och Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag och Azure offentliga tjänster, till exempel lagring och SQL-databas är tillgängliga via Microsoft-peering. Azure offentliga tjänster är valbara på grundval av per region och kan inte definieras per offentlig tjänst. 

När Microsoft-peering har konfigurerats på en ExpressRoute-krets och ett flöde filter är ansluten, visas alla prefix som har valts för dessa tjänster i BGP-sessioner som upprättas. Ett värde för BGP-gemenskapen är kopplad till varje prefix för att identifiera den tjänst som erbjuds via prefixet. En lista över BGP community-värden och de tjänster som de mappas till finns [BGP communities](expressroute-routing.md#bgp).

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
  - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och ha kretsen aktiveras med anslutningsleverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.
  - [Skapa Microsoft-peering](expressroute-circuit-peerings.md) om du hanterar BGP-sessionen direkt. Har anslutningsleverantören etablera Microsoft-peering för kretsen.

-  Du måste skapa och konfigurera ett filter för vägen.
    - Identifiera tjänsterna du med att använda via Microsoft-peering
    - Identifiera listan över BGP community-värden som är kopplade till tjänsterna
    - Skapa en regel för att tillåta listan över prefix matchande BGP community-värden

-  Du måste koppla filtret vägen till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera måste du kontrollera att du uppfyller följande kriterier:

 - Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

  > [!NOTE]
  > Hämta den senaste versionen från PowerShell-galleriet i stället för med hjälp av installationsprogrammet. Installationsprogrammet stöder för närvarande inte cmdletarna som krävs.
  > 

 - Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.

 - Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.

 - Du måste ha ett aktivt Microsoft-peering. Följ anvisningarna på [skapa och ändra peering konfiguration](expressroute-circuit-peerings.md)

### <a name="log-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Innan du påbörjar konfigurationen måste du logga in på ditt Azure-konto. Den här cmdleten uppmanar dig att ange inloggningsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell.

Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Ange den prenumeration som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-gemenskapen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över BGP community värden

Använd följande cmdlet för att hämta listan över BGP community-värden som är kopplad till tjänster som är tillgänglig via Microsoft-peering och i listan över prefix som är kopplade till dem:

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Skapa en lista över de värden som du vill använda

Se en lista över BGP community-värden som du vill använda i filtret vägen. Exempelvis är värdet för BGP-community för Dynamics 365-tjänster 12076:5040.

## <a name="filter"></a>Steg 2: Skapa ett flöde filter och en filterregeln

En vägfilter kan ha endast en regel och regeln måste vara av typen ”Tillåt'. Den här regeln kan ha en lista över BGP community-värden som är kopplade till den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett vägfilter

Först skapa vägen filtret. Kommandot 'Ny AzureRmRouteFilter' skapar bara en väg filter resurs. När du skapar resursen, måste du sedan skapa en regel och koppla den till filtreringsobjekt vägen. Kör följande kommando för att skapa en väg filter resurs:

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Skapa en regel för filter

Du kan ange en uppsättning BGP communities som en kommaavgränsad lista som visas i exemplet. Kör följande kommando för att skapa en ny regel:
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Lägga till regeln route-filtret

Kör följande kommando för att lägga till filtret regeln vägfiltret:
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Steg 3: Koppla filtret vägen till en ExpressRoute-krets

Kör följande kommando för att koppla filtret vägen till ExpressRoute-krets, förutsatt att du har endast Microsoft-peering:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Vanliga åtgärder

### <a name="getproperties"></a>Att hämta egenskaperna för en vägfilter

Använd följande steg för att hämta egenskaperna för en vägfilter:

1. Kör följande kommando för att hämta filter routeresurs:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Hämta vägen filterregler för resursen route-filter genom att köra följande kommando:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Uppdatera egenskaperna för en vägfilter

Om vägfiltret redan är kopplad till en krets sprida uppdateringar i listan BGP community automatiskt lämpliga prefix annons ändringar via befintliga BGP-sessioner. Du kan uppdatera BGP community listan över vägfiltret med följande kommando:

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Att ta bort ett filter för vägen från en ExpressRoute-krets

När en vägfilter är frånkopplat från ExpressRoute-kretsen har inget prefix annonserats via BGP-sessionen. Du kan koppla från ett flöde filter från en ExpressRoute-krets med följande kommando:
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Ta bort ett vägfilter

Du kan bara ta bort ett filter för vägen om det inte är kopplat till någon krets. Se till att filtret vägen inte är ansluten till alla krets innan du försöker ta bort den. Du kan ta bort ett filter för vägen med följande kommando:

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
