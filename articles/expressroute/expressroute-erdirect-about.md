---
title: Om Azure ExpressRoute Direct
description: Den här sidan ger en översikt över ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083592"
---
# <a name="about-expressroute-direct"></a>Om ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser som är strategiskt fördelade över hela världen. ExpressRoute Direct ger dubbla 100 Gbit/s- eller 10 Gbit/s-anslutning, som stöder aktiv/aktiv anslutning i stor skala.

Viktiga funktioner som ExpressRoute Direct tillhandahåller är, men är inte begränsade till:

* Stora datainmatningar till tjänster som Storage och Cosmos DB
* Fysisk isolering för branscher som är reglerade och kräver dedikerad och isolerad konnektivitet som: Bank, regering och Detaljhandel
* Detaljerad kontroll över kretsfördelning utifrån affärsenheter

## <a name="onboard-to-expressroute-direct"></a>Ombord på ExpressRoute Direct

Innan du använder ExpressRoute Direct måste du först registrera din prenumeration. Om du vill registrera <ExpressRouteDirect@microsoft.com> dig skickar du ett e-postmeddelande till med ditt prenumerations-ID, inklusive följande information:

* Scenarier som du vill utföra med **ExpressRoute Direct**
* Platsinställningar – se [Partner och peering-platser](expressroute-locations-providers.md) för en fullständig lista över alla platser
* Tidslinje för implementering
* Alla andra frågor

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute med hjälp av en tjänsteleverantör och ExpressRoute Direct

| **ExpressRoute med hjälp av en tjänsteleverantör** | **ExpressRoute Direct** | 
| --- | --- |
| Använder tjänsteleverantörer för att möjliggöra snabb introduktion och anslutning till befintlig infrastruktur | Kräver 100 Gbps/10 Gbps-infrastruktur och full hantering av alla lager
| Integreras med hundratals leverantörer, inklusive Ethernet och MPLS | Direkt/särskild kapacitet för reglerade industrier och massiv datainmatning |
| Kretsar SKU:er från 50 Mbit/s till 10 Gbit/s | Kunden kan välja en kombination av följande kretsskrot på 100 Gbps ExpressRoute Direct: <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> Kunden kan välja en kombination av följande kretsskrot på 10 Gbit/s ExpressRoute Direct:<ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>
| Optimerad för en enda klient | Optimerad för en enda hyresgäst med flera affärsenheter och flera arbetsmiljöer

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct kretsar

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure och Office 365.

Varje peering-plats har åtkomst till Microsofts globala nätverk och kan som standard komma åt alla regioner i en geopolitisk zon och kan komma åt alla globala regioner med en premiumkrets.  

Funktionaliteten i de flesta scenarier motsvarar kretsar som använder en ExpressRoute-tjänsteleverantör för att fungera. För att stödja ytterligare granularitet och nya funktioner som erbjuds med ExpressRoute Direct finns det vissa viktiga funktioner som finns på ExpressRoute Direct Circuits.

## <a name="circuit-skus"></a>Krets SKU:er

ExpressRoute Direct stöder massiva scenarier för datainmatning i Azure-lagring och andra stordatatjänster. ExpressRoute-kretsar på 100 Gbps ExpressRoute Direct stöder nu även **40 Gbit/s** och **100 Gbit/s-kretsskrokar.** De fysiska portparen är endast **100 eller 10 Gbit/s** och kan ha flera virtuella kretsar. Kretsstorlekar:

| **100 Gbit/s ExpressRoute Direkt** | **10 Gbit/s ExpressRoute Direkt** | 
| --- | --- |
| **Prenumererad bandbredd:** 200 Gbps | **Prenumererad bandbredd:** 20 Gbit/s |
| <ul><li>5 Gbit/s</li><li>10 Gbit/s</li><li>40 Gbit/s</li><li>100 Gbit/s</li></ul> | <ul><li>1 Gbit/s</li><li>2 Gbit/s</li><li>5 Gbit/s</li><li>10 Gbit/s</li></ul>

## <a name="technical-requirements"></a>Tekniska krav

* MSEE-gränssnitt (Microsoft Enterprise Edge Router):
    * Dubbla 10- eller 100 Gigabit Ethernet-portar endast mellan routerpar
    * LR-fiberanslutning i ett läge
    * IPv4 och IPv6
    * IP MTU 1500 byte

* Switch/Router Layer 2/Layer 3-anslutning:
    * Måste ha stöd för tagginkapsling med 1 802.1Q (Dot1Q) eller två Tag 802.1Q-tagginkapsling
    * Ethertype = 0x8100
    * Måste lägga till den yttre VLAN-taggen (STAG) baserat på det VLAN-ID som anges av Microsoft - *gäller endast på QinQ*
    * Måste ha stöd för flera BGP-sessioner (VLAN) per port och enhet
    * IPv4- och IPv6-anslutning. *För IPv6 skapas inget ytterligare undergränssnitt. IPv6-adressen läggs till i befintligt undergränssnitt*. 
    * Valfritt: [Stöd för BFD (Bidirectional Forwarding Detection),](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) som konfigureras som standard på alla privata peerings på ExpressRoute-kretsar

## <a name="vlan-tagging"></a>VLAN-märkning

ExpressRoute Direct stöder både QinQ- och Dot1Q VLAN-taggning.

* **QinQ VLAN-taggning** möjliggör isolerade routningsdomäner enligt ExpressRoute-kretsbasis. Azure allokerar dynamiskt en S-Tagg vid att skapa krets och kan inte ändras. Varje peering på kretsen (Privat och Microsoft) kommer att använda en unik C-Tag som VLAN. C-Tag behöver inte vara unikt över kretsar på ExpressRoute Direct-portarna.

* **Dot1Q VLAN-taggning** möjliggör en enda taggad VLAN på basis av Per ExpressRoute Direct-portpar. En C-Tag som används på en peering måste vara unik över alla kretsar och peerings på ExpressRoute Direct-portparet.

## <a name="workflow"></a>Arbetsflöde

[![Arbetsflöde](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct tillhandahåller samma serviceavtal i företagsklass med aktiva/aktiva redundanta anslutningar till Microsofts globala nätverk. ExpressRoute-infrastrukturen är redundant och anslutningen till Microsoft Global Network är överflödig och varierad och skalar därefter med kundernas krav. 

## <a name="next-steps"></a>Nästa steg

[Konfigurera ExpressRoute Direct](expressroute-howto-erdirect.md)
