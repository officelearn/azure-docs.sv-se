---
title: Om Azure ExpressRoute Direct | Microsoft Docs
description: Den här sidan innehåller en översikt över ExpressRoute Direct (förhandsversion)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: c33bec76fe17336221c873778c2993d75fec81e8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962240"
---
# <a name="about-expressroute-direct-preview"></a>Dirigera om ExpressRoute (förhandsversion)

ExpressRoute Direct ger kunderna möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser strategiskt distribueras över hela världen. ExpressRoute Direct innehåller dubbla 100Gbps anslutning som har stöd för aktiv/aktiv-anslutningar i stor skala. 

Viktiga funktioner som tillhandahåller ExpressRoute Direct omfattar, men är inte begränsade till:

* Omfattande Datapåfyllning till tjänster som Storage och Cosmos DB 
* Fysisk isolering för branscher som regleras och krävs dedikerad och isolerade anslutning som: Bank, myndigheter och detaljhandel 
* Detaljerad kontroll över krets distribution baserad på affärsenhet

> [!IMPORTANT]
> ExpressRoute Direct förhandsvisas just nu.
>
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Registrera dig för förhandsversionen

Innan du kan utnyttja ExpressRoute direct, måste du först registrera din prenumeration i förhandsversionen. Skicka ett e-postmeddelande till <ExpressRouteDirect@microsoft.com> med ditt prenumerations-ID om du vill registrera dig. ExpressRoute Direct är en funktion i företagsklass. Ange ytterligare information:

* Scenarier som du vill utföra med **ExpressRoute Direct**
* Inställningar för plats - finns i [Partners och peeringplatser](expressroute-locations-providers.md) för en fullständig lista över alla platser
* Tidslinje för implementering
* Frågor om tjänsterna

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute med en tjänstleverantör och ExpressRoute Direct

| **ExpressRoute med en tjänstleverantör** | **ExpressRoute Direct** | 
| --- | --- | 
| Använder tjänstleverantör för att möjliggöra snabb onboarding och anslutning till befintlig infrastruktur | Kräver 100Gbps infrastruktur och fullständig hantering av alla lager
| Kan integreras med hundratals av inklusive Ethernet och MPLS-leverantörer | Direct eller dedikerad kapacitet för reglerade branscher och enorma datainmatning | 
| Kretsar SKU: er från 50 Mbit/s – 10 Gbit/s | Kretsar SKU: er från 1 Gbit/s 100Gbps
| Optimerad för enskild klient | Optimerad för enskild klient-/ molntjänstleverantörer / flera affärsenheter

## <a name="expressroute-direct-circuits"></a>Direct för ExpressRoute-kretsar

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  

Varje peeringplats har åtkomst till Microsofts globala nätverk och kan komma åt alla regioner i en geopolitiska zon som standard och har åtkomst till alla globala regioner med en premium-krets.  

Funktionerna i de flesta fall motsvarar kretsar som använder en ExpressRoute-leverantör för att fungera. För att stödja mer detaljrikedom och nya funktioner som erbjuds med hjälp av ExpressRoute Direct, finns det vissa viktiga funktioner som finns på direkt ExpressRoute-kretsar.

## <a name="circuit-skus"></a>Kretsen SKU: er

ExpressRoute Direct stöder scenarier för inmatning av massiva data i Azure storage och andra tjänster för stordata. ExpressRoute circuits på ExpressRoute Direct nu också stöd för **40G** och **100G** krets SKU: er. 

## <a name="vlan-tagging"></a>VLAN-märkning

ExpressRoute Direct stöder både QinQ och Dot1Q VLAN-märkning.

* **QinQ VLAN-märkning** möjliggör isolerade routningsdomäner på en per ExpressRoute-krets basis. Azure allokerar en S-tagg på används för att skapa dynamiskt och kan inte ändras. Varje peering i kretsen (privat och Microsoft) ska använda en unik C-tagg som VLAN. C-taggen krävs inte vara unikt för kretsar på ExpressRoute Direct-portar. 

* **Dot1Q VLAN-märkning** tillåter för en enda taggade VLAN på en per ExpressRoute direkt port par basis. En C-tagg som används på en peering måste vara unikt för alla kretsar och peer-kopplingar på ExpressRoute direkt port paret.

## <a name="workflow"></a>Arbetsflöde

![arbetsflöde](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>SLA

ExpressRoute Direct ger redundanta aktiv/aktiv-anslutningar till Microsofts globala nätverk av samma serviceavtal i företagsklass. ExpressRoute-infrastruktur är redundant och anslutning till Microsofts globala nätverk är redundant och skilda och kan skalas på lämpligt sätt med kundbehov. I förhandsversionen finns inget serviceavtal och bör beaktas endast för arbetsbelastningar utanför produktionsmiljön.

## <a name="next-steps"></a>Nästa steg

[Konfigurera ExpressRoute Direct](expressroute-howto-erdirect.md)