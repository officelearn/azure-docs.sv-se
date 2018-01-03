---
title: "Konfigurera filter för routning för Azure ExpressRoute Microsoft peering: CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar filter för routning för Microsoft-Peering med Azure CLI"
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurera filter för routning för Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure-portalen](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Vägfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälpa dig att konfigurera och hantera filter för routning för ExpressRoute-kretsar.

Dynamics 365-tjänster och Office 365-tjänster som Exchange Online, SharePoint Online och Skype för företag, är tillgängliga via Microsoft-peering. När Microsoft-peering konfigureras i en ExpressRoute-krets, visas alla prefix som är relaterade till tjänsterna i BGP-sessioner som upprättas. Ett värde för BGP-gemenskapen är kopplad till varje prefix för att identifiera den tjänst som erbjuds via prefixet. En lista över BGP community-värden och de tjänster som de mappas till finns [BGP communities](expressroute-routing.md#bgp).

Om du behöver ansluta till alla tjänster har ett stort antal prefix annonserats via BGP. Detta ökar avsevärt storleken på vägtabeller upprätthålls av routrar i nätverket. Om du planerar att använda endast en delmängd av tjänster som erbjuds via Microsoft peering kan du minska storleken på din vägtabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att använda filter för routning på BGP-communities. Detta är ett vanligt nätverk förfarande och används ofta i flera nätverk.

* Definiera filter för Routning och koppla dem till ExpressRoute-kretsen. Ett filter för vägen är en ny resurs där du kan välja i listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar bara skicka listan över prefix som hör till de tjänster som identifierats i filtret vägen.

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

* Du måste ha en aktiv ExpressRoute-krets som har Microsoft peering etablerade. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  * [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och ha kretsen aktiveras med anslutningsleverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.
  * [Skapa Microsoft-peering](howto-routing-cli.md) om du hanterar BGP-sessionen direkt. Har anslutningsleverantören etablera Microsoft-peering för kretsen.

* Du måste skapa och konfigurera ett filter för vägen.
  * Identifiera tjänsterna du med att använda via Microsoft-peering
  * Identifiera listan över BGP community-värden som är kopplade till tjänsterna
  * Skapa en regel för att tillåta listan över prefix matchande BGP community-värden

* Du måste koppla filtret vägen till ExpressRoute-kretsen.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

* Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.

* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverade tillstånd.

* Du måste ha ett aktivt Microsoft-peering. Följ anvisningarna på [skapa och ändra peering konfiguration](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välja din prenumeration

Påbörja din konfiguration genom att logga in på ditt Azure-konto. Använd följande exempel för att ansluta:

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

## <a name="prefixes"></a>Steg 1: Hämta en lista över prefix och värden för BGP-gemenskapen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Hämta en lista över BGP community värden

Använd följande cmdlet för att hämta listan över BGP community-värden som är kopplad till tjänster som är tillgänglig via Microsoft-peering och i listan över prefix som är kopplade till dem:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Skapa en lista över de värden som du vill använda

Se en lista över BGP community-värden som du vill använda i filtret vägen. Exempelvis är värdet för BGP-community för Dynamics 365-tjänster 12076:5040.

## <a name="filter"></a>Steg 2: Skapa ett flöde filter och en filterregeln

En vägfilter kan ha endast en regel och regeln måste vara av typen ”Tillåt'. Den här regeln kan ha en lista över BGP community-värden som är kopplade till den.

### <a name="1-create-a-route-filter"></a>1. Skapa ett vägfilter

Först skapa vägen filtret. Kommandot 'az nätverket route-filter skapa' skapar bara en väg filter resurs. När du skapar resursen, måste du sedan skapa en regel och koppla den till filtreringsobjekt vägen. Kör följande kommando för att skapa en väg filter resurs:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Skapa en regel för filter

Kör följande kommando för att skapa en ny regel:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Steg 3: Koppla filtret vägen till en ExpressRoute-krets

Kör följande kommando för att koppla filtret vägen till ExpressRoute-kretsen:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Vanliga åtgärder

### <a name="getproperties"></a>Att hämta egenskaperna för en vägfilter

Använd följande kommando för att hämta egenskaperna för en vägfilter:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Uppdatera egenskaperna för en vägfilter

Om vägfiltret redan är kopplad till en krets sprida uppdateringar i listan BGP community automatiskt lämpliga prefix annons ändringar via befintliga BGP-sessioner. Du kan uppdatera BGP community listan över vägfiltret med följande kommando:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Att ta bort ett filter för vägen från en ExpressRoute-krets

När en vägfilter är frånkopplat från ExpressRoute-kretsen har inget prefix annonserats via BGP-sessionen. Du kan koppla från ett flöde filter från en ExpressRoute-krets med följande kommando:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Ta bort ett vägfilter

Du kan bara ta bort ett filter för vägen om det inte är kopplat till någon krets. Se till att filtret vägen inte är ansluten till alla krets innan du försöker ta bort den. Du kan ta bort ett filter för vägen med följande kommando:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
