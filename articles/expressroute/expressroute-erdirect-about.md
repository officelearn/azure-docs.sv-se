---
title: Om ExpressRoute Direct - Azure | Microsoft Docs
description: Den här sidan innehåller en översikt över ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: fb9dc5116ba23d57c7f2fe543e734759e8bbcc7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367647"
---
# <a name="about-expressroute-direct"></a>Om ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser strategiskt distribueras över hela världen. ExpressRoute Direct innehåller dubbla 100 Gbit/s-anslutningar som har stöd för aktiv/aktiv-anslutningar i stor skala.

Viktiga funktioner som tillhandahåller ExpressRoute Direct inkludera, men inte begränsat till:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för branscher som regleras och kräver dedikerade och isolerad anslutning som: banker, myndigheter och detaljhandeln
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

## <a name="onboard-to-expressroute-direct"></a>Kom igång med ExpressRoute Direct

Innan du använder ExpressRoute Direct, måste du först registrera din prenumeration. Om du vill registrera, skicka ett e- <ExpressRouteDirect@microsoft.com> med ditt prenumerations-ID, inklusive följande information:

* Scenarier som du vill utföra med **ExpressRoute Direct**
* Inställningar för plats - finns i [Partners och peeringplatser](expressroute-locations-providers.md) för en fullständig lista över alla platser
* Tidslinje för implementering
* Andra frågor

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute med en tjänstleverantör och ExpressRoute Direct

| **ExpressRoute med en tjänstleverantör** | **ExpressRoute Direct** | 
| --- | --- |
| Använder leverantörer för att möjliggöra snabb onboarding och anslutning till befintlig infrastruktur | Kräver 100 Gbit/s infrastruktur och fullständig hantering av alla lager
| Kan integreras med hundratals av inklusive Ethernet och MPLS-leverantörer | Direct eller dedikerad kapacitet för reglerade branscher och enorma datainmatning |
| Kretsar SKU: er från 50 Mbit/s till 10 Gbit/s | Kunden kan välja en kombination av följande kretsen SKU: er: 5 Gbit/s, 10 Gbit/s, 40 Gbit/s, 100 Gbit/s - begränsat till högst 200 Gbit/s
| Optimerad för enskild klient | Optimerad för enskild klient-/ molntjänstleverantörer / flera affärsenheter

## <a name="expressroute-direct-circuits"></a>Direct för ExpressRoute-kretsar

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  

Varje peeringplats har åtkomst till Microsofts globala nätverk och kan komma åt alla regioner i en geopolitiska zon som standard och har åtkomst till alla globala regioner med en premium-krets.  

Funktionerna i de flesta fall motsvarar kretsar som använder en ExpressRoute-leverantör för att fungera. För att stödja mer detaljrikedom och nya funktioner som erbjuds med hjälp av ExpressRoute Direct, finns det vissa viktiga funktioner som finns på direkt ExpressRoute-kretsar.

## <a name="circuit-skus"></a>Kretsen SKU: er

ExpressRoute Direct stöder scenarier för inmatning av massiva data i Azure storage och andra tjänster för stordata. ExpressRoute circuits på ExpressRoute Direct nu också stöd för **40 Gbit/s** och **100 Gbit/s** krets SKU: er. De fysiska portpar är **100 Gbit/s** endast och kan ha flera virtuella kretsar med bandbredder 5 Gbit/s, 10 Gbit/s, 40 Gbit/s, 100 Gbit/s - upp till 200 GB/s i vilken kombination som helst. 

## <a name="vlan-tagging"></a>VLAN-märkning

ExpressRoute Direct stöder både QinQ och Dot1Q VLAN-märkning.

* **QinQ VLAN-märkning** möjliggör isolerade routningsdomäner på en per ExpressRoute-krets basis. Azure allokerar en S-tagg på används för att skapa dynamiskt och kan inte ändras. Varje peering i kretsen (privat och Microsoft) ska använda en unik C-tagg som VLAN. C-taggen krävs inte vara unikt för kretsar på ExpressRoute Direct-portar.

* **Dot1Q VLAN-märkning** tillåter för en enda taggade VLAN på en per ExpressRoute direkt port par basis. En C-tagg som används på en peering måste vara unikt för alla kretsar och peer-kopplingar på ExpressRoute direkt port paret.

## <a name="workflow"></a>Arbetsflöde

[![Arbetsflöde](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct ger redundanta aktiv/aktiv-anslutningar till Microsofts globala nätverk av samma serviceavtal i företagsklass. ExpressRoute-infrastruktur är redundant och anslutning till Microsofts globala nätverk är redundant och skilda och kan skalas på lämpligt sätt med kundbehov. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera ExpressRoute Direct](expressroute-howto-erdirect.md)
