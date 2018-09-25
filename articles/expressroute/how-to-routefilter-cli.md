---
title: 'Konfigurera routningsfilter för Microsoft för Azure ExpressRoute-peering: CLI | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar routningsfilter för Microsoft-Peering med hjälp av Azure CLI
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: 29cbe1686888a87fca6ddde957a1cbd35ba3df26
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968704"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurera routningsfilter för Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en underuppsättning av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera flödesfilter för ExpressRoute-kretsar.

Dynamics 365-tjänster och Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag, är tillgängliga via Microsoft-peering. När Microsoft-peering har konfigurerats i en ExpressRoute-krets, visas alla prefix som är relaterade till dessa tjänster i BGP-sessioner upprättas. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över BGP community-värden och de tjänster som de mappas till finns i [BGP-communities](expressroute-routing.md#bgp).

Om du behöver anslutning till alla tjänster har ett stort antal prefix annonseras via BGP. Detta ökar avsevärt storleken på routningstabeller som underhålls av routrar i nätverket. Om du planerar att använda en underuppsättning av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att tillämpa flödesfilter på BGP-communities. Detta är ett vanligt nätverk förfarande och används ofta i många nätverk.

* Definiera vägfilter och tillämpa dem på din ExpressRoute-krets. Ett flödesfilter är en ny resurs där du kan välja i listan över tjänster som du tänker använda via Microsoft-peering. ExpressRoute-routrar Skicka endast lista över prefix som hör till de tjänster som identifierats i flödesfiltret.

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

* Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerade. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  * [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.
  * [Skapa Microsoft-peering](howto-routing-cli.md) om du hanterar BGP-sessionen direkt. Eller låt anslutningsleverantören etablera Microsoft-peering för din krets.

* Du måste skapa och konfigurera ett flödesfilter.
  * Identifiera tjänsterna du med att använda via Microsoft-peering
  * Identifiera listan över BGP community-värden som är associerade med tjänsterna
  * Skapa en regel för att tillåta Prefixlistan matchande värden för BGP-community

* Du måste koppla flödesfiltret till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om hur du installerar CLI-kommandon finns i [installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

* Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd.

* Du måste ha en aktiv Microsoft-peering. Följ anvisningarna i [skapa och ändra peering-konfigurationen](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

Du börjar din konfiguration genom att logga in på ditt Azure-konto. Använd följande exempel för att ansluta:

```azurecli
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Steg 1: Hämta en lista över prefix och BGP community-värden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över BGP community-värden

Använd följande cmdlet för att hämta listan över BGP community-värden som är associerade med tjänster som är tillgängliga via Microsoft-peering och listan med prefix som är associerade med dem:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Skapa en lista över de värden som du vill använda

Se en lista över BGP community-värden som du vill använda i flödesfiltret. Exempelvis är 12076:5040 i BGP-community värde för Dynamics 365-tjänster.

## <a name="filter"></a>Steg 2: Skapa ett flödesfilter och en regel för filter

Ett flödesfilter kan ha endast en regel och regeln måste vara av typen 'Tillåt'. Den här regeln kan ha en lista över BGP community-värden som är associerade med den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett flödesfilter

Skapa först flödesfiltret. Kommandot ”az network route-filter create” skapar bara en resurs för route-filter. När du skapar resursen kan du sedan skapa en regel och koppla den till objektet route-filter. Kör följande kommando för att skapa en resurs för route-filter:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Skapa en regel för filter

Kör följande kommando för att skapa en ny regel:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Steg 3: Koppla flödesfiltret till en ExpressRoute-krets

Kör följande kommando för att koppla flödesfiltret till ExpressRoute-krets:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Vanliga åtgärder

### <a name="getproperties"></a>Att hämta egenskaperna för ett flödesfilter

Om du vill hämta egenskaperna för ett flödesfilter, använder du följande kommando:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Att uppdatera egenskaperna för ett flödesfilter

Om flödesfiltret redan är ansluten till en krets sprida uppdateringar till BGP community-lista automatiskt lämpliga prefix annons ändringar genom etablerade BGP-sessioner. Du kan uppdatera BGP community-lista över dina flödesfilter med följande kommando:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Att koppla från ett flödesfilter från en ExpressRoute-krets

När ett flödesfilter är frånkopplat från ExpressRoute-krets, har inget prefix annonserats via BGP-sessionen. Du kan koppla från ett flödesfilter från en ExpressRoute-krets med hjälp av följande kommando:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Att ta bort ett flödesfilter

Du kan bara ta bort ett flödesfilter om den inte är ansluten till alla kretsar. Se till att flödesfiltret inte är ansluten till alla kretsar innan du försöker ta bort den. Du kan ta bort ett flödesfilter med följande kommando:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
