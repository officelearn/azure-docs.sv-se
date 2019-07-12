---
title: Om ExpressRoute Direct - Azure | Microsoft Docs
description: Den här sidan innehåller en översikt över ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807487"
---
# <a name="about-expressroute-direct"></a>Om ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser strategiskt distribueras över hela världen. ExpressRoute Direct innehåller dubbla 100 Gbit/s eller 10 Gbit/s-anslutningar som har stöd för aktiv/aktiv-anslutningar i stor skala.

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
| Använder leverantörer för att möjliggöra snabb onboarding och anslutning till befintlig infrastruktur | Kräver 100 Gbit/s/10 Gbit/s infrastruktur och fullständig hantering av alla lager
| Kan integreras med hundratals av inklusive Ethernet och MPLS-leverantörer | Direct eller dedikerad kapacitet för reglerade branscher och enorma datainmatning |
| Kretsar SKU: er från 50 Mbit/s till 10 Gbit/s | Kunden kan välja en kombination av följande SKU: er på 100 Gbit/s ExpressRoute-krets direkt: <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> Kunden kan välja en kombination av följande SKU: er på 10 Gbit/s ExpressRoute-krets direkt:<ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>
| Optimerad för enskild klient | Optimerad för enskild klient-/ molntjänstleverantörer / flera affärsenheter

## <a name="expressroute-direct-circuits"></a>Direct för ExpressRoute-kretsar

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  

Varje peeringplats har åtkomst till Microsofts globala nätverk och kan komma åt alla regioner i en geopolitiska zon som standard och har åtkomst till alla globala regioner med en premium-krets.  

Funktionerna i de flesta fall motsvarar kretsar som använder en ExpressRoute-leverantör för att fungera. För att stödja mer detaljrikedom och nya funktioner som erbjuds med hjälp av ExpressRoute Direct, finns det vissa viktiga funktioner som finns på direkt ExpressRoute-kretsar.

## <a name="circuit-skus"></a>Kretsen SKU: er

ExpressRoute Direct stöder scenarier för inmatning av massiva data i Azure storage och andra tjänster för stordata. ExpressRoute-kretsar på 100 Gbit/s ExpressRoute Direct stöder nu även **40 Gbit/s** och **100 Gbit/s** krets SKU: er. De fysiska portpar är **100 eller 10 Gbit/s** endast och kan ha flera virtuella kretsar. Kretsen storlekar:

| **100 Gbit/s ExpressRoute Direct** | **10 Gbit/s ExpressRoute Direct** | 
| --- | --- |
| **Prenumererar på bandbredd**: 200 Gbit/s | **Prenumererar på bandbredd**: 20 Gbit/s |
| <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> | <ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>

## <a name="technical-requirements"></a>Tekniska krav

* Gränssnitt för Microsoft Enterprise Edge-routern (MSEE):
    * Dubbla 10 eller 100 Gigabit Ethernet-portar endast över router-par
    * Den enda läge LR Fiber anslutning
    * IPv4 och IPv6
    * IP-MTU 1500 byte

* Växeln/Router Layer 2/Layer 3-anslutningen:
    * Måste ha stöd för 1 802.1Q (Dot1Q)-tagg eller två taggen 802.1Q (QinQ) tagga inkapsling
    * Ethertype = 0x8100
    * Lägga till den yttre VLAN-tagg (STAG) baserat på det VLAN-ID som angetts av Microsoft - *gäller endast på QinQ*
    * Måste ha stöd för flera BGP-sessioner (VLAN) per port och enhet
    * IPv4 och IPv6-anslutning. *Inget ytterligare underordnade gränssnitt kommer att skapas för IPv6. IPv6-adressen kommer att läggas till befintliga underordnade gränssnittet*. 
    * Valfritt: [Dubbelriktad vidarebefordran identifiering (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) stöder, som är konfigurerad som standard på alla privata Peerings på ExpressRoute-kretsar

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
