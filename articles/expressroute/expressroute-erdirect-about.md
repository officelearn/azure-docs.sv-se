---
title: Om ExpressRoute Direct - Azure | Microsoft Docs
description: Den här sidan ger en översikt över ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: a294c444f10719f69716b25b97cd137874a3e0be
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954156"
---
# <a name="about-expressroute-direct"></a>Om ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser strategiskt distribueras över hela världen. ExpressRoute Direct tillhandahåller dubbla 100 Gbit/s-anslutningar på 10 Gbit/s, som stöder aktiv/aktiv anslutning i stor skala.

Viktiga funktioner som tillhandahåller ExpressRoute Direct inkludera, men inte begränsat till:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för branscher som är reglerade och kräver dedikerad och isolerad anslutning som: banker, myndigheter och detaljhandeln
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

## <a name="onboard-to-expressroute-direct"></a>Publicera till ExpressRoute Direct

Innan du använder ExpressRoute Direct måste du först registrera din prenumeration. Om du vill registrera, skicka ett e- <ExpressRouteDirect@microsoft.com> med ditt prenumerations-ID, inklusive följande information:

* Scenarier som du vill utföra med **ExpressRoute Direct**
* Inställningar för plats - finns i [Partners och peeringplatser](expressroute-locations-providers.md) för en fullständig lista över alla platser
* Tidslinje för implementering
* Andra frågor

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute med en tjänstleverantör och ExpressRoute Direct

| **ExpressRoute med en tjänstleverantör** | **ExpressRoute Direct** | 
| --- | --- |
| Använder leverantörer för att möjliggöra snabb onboarding och anslutning till befintlig infrastruktur | Kräver en infrastruktur på 100 Gbit/s/10 Gbit/s och fullständig hantering av alla skikt
| Kan integreras med hundratals av inklusive Ethernet och MPLS-leverantörer | Direct eller dedikerad kapacitet för reglerade branscher och enorma datainmatning |
| Kretsar SKU: er från 50 Mbit/s till 10 Gbit/s | Kunden kan välja en kombination av följande krets-SKU: er på 100 Gbit/s ExpressRoute Direct: <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> Kunden kan välja en kombination av följande krets-SKU: er på 10 Gbit/s ExpressRoute Direct:<ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>
| Optimerad för enskild klient | Optimerad för en enskild klient med flera affär senheter och flera arbets miljöer

## <a name="expressroute-direct-circuits"></a>Direct för ExpressRoute-kretsar

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  

Varje peeringplats har åtkomst till Microsofts globala nätverk och kan komma åt alla regioner i en geopolitiska zon som standard och har åtkomst till alla globala regioner med en premium-krets.  

Funktionerna i de flesta fall motsvarar kretsar som använder en ExpressRoute-leverantör för att fungera. För att stödja mer detaljrikedom och nya funktioner som erbjuds med hjälp av ExpressRoute Direct, finns det vissa viktiga funktioner som finns på direkt ExpressRoute-kretsar.

## <a name="circuit-skus"></a>Kretsen SKU: er

ExpressRoute Direct stöder scenarier för inmatning av massiva data i Azure storage och andra tjänster för stordata. ExpressRoute-kretsar på 100 Gbit/s ExpressRoute Direct stöder nu även **40 Gbit/** s och **100 Gbit/s** krets SKU: er. De fysiska portarna är **100 eller 10 Gbit/s** och kan ha flera virtuella kretsar. Krets storlekar:

| **100 Gbit/s ExpressRoute Direct** | **10 Gbit/s ExpressRoute Direct** | 
| --- | --- |
| **Prenumererad bandbredd**: 200 Gbit/s | **Prenumererad bandbredd**: 20 Gbit/s |
| <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> | <ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>

## <a name="technical-requirements"></a>Tekniska krav

* Microsoft MSEE: N-gränssnitt (Enterprise Edge router):
    * Dubbla 10-eller 100 Gigabit Ethernet-portar endast över router-par
    * LR fiber anslutning med enkel läge
    * IPv4 och IPv6
    * IP-MTU 1500 byte

* Anslutning/router skikt 2/Layer 3-anslutning:
    * Måste ha stöd för 1 802.1 Q (Dot1Q)-tagg eller två tag 802.1 Q (QinQ) tagg-inkapsling
    * Ethertype = 0x8100
    * Måste lägga till den yttre VLAN-taggen (STAG) baserat på det VLAN-ID som anges av Microsoft- *endast tillgängligt på QinQ*
    * Måste ha stöd för flera BGP-sessioner (VLAN) per port och enhet
    * IPv4-och IPv6-anslutning. *Inga ytterligare under gränssnitt kommer att skapas för IPv6. IPv6-adressen kommer att läggas till i det befintliga*under gränssnittet. 
    * Valfritt: Stöd för [BFD-stöd (dubbelriktad vidarebefordring)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) som är konfigurerat som standard för alla privata peer-kopplingar på ExpressRoute-kretsar

## <a name="vlan-tagging"></a>VLAN-märkning

ExpressRoute Direct stöder både QinQ och Dot1Q VLAN-märkning.

* **QinQ VLAN-märkning** möjliggör isolerade routningsdomäner på en per ExpressRoute-krets basis. Azure allokerar en S-tagg på används för att skapa dynamiskt och kan inte ändras. Varje peering i kretsen (privat och Microsoft) ska använda en unik C-tagg som VLAN. C-taggen krävs inte vara unikt för kretsar på ExpressRoute Direct-portar.

* **Dot1Q VLAN-märkning** tillåter för en enda taggade VLAN på en per ExpressRoute direkt port par basis. En C-tagg som används på en peering måste vara unikt för alla kretsar och peer-kopplingar på ExpressRoute direkt port paret.

## <a name="workflow"></a>Arbetsflöde

[![arbets flöde](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct ger redundanta aktiv/aktiv-anslutningar till Microsofts globala nätverk av samma serviceavtal i företagsklass. ExpressRoute-infrastruktur är redundant och anslutning till Microsofts globala nätverk är redundant och skilda och kan skalas på lämpligt sätt med kundbehov. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera ExpressRoute Direct](expressroute-howto-erdirect.md)
