---
title: Om Azure Edge-zoner – förhandsversion
description: Lär dig mer om edge computing erbjudanden från Microsoft.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 03/31/2020
ms.author: ganesr
ms.openlocfilehash: d04032075166d2edd273c7f22bfb0d54aeccd8b3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545824"
---
# <a name="about-azure-edge-zones---preview"></a>Om Azure Edge-zoner – förhandsversion

Azure Edge Zones är en familj av erbjudanden från Microsoft Azure som möjliggör databearbetning nära användaren. Du kan distribuera virtuella datorer, behållare och andra utvalda Azure-tjänster till Edge Zoner för att hantera kraven på låg latens och högt dataflöde för program.

Typiska användningsfallsscenarier för Edge-zoner är:

- Kommandot och kontrollen i realtid inom robotteknik
- Analys och inferencing i realtid med artificiell intelligens och maskininlärning
- Maskinseende
- Fjärrrendering för mixade verklighets- och VDI-scenarier
- Uppslukande spel för flera spelare
- Direktuppspelning av media och leverans av innehåll
- Övervakning och säkerhet

Azure Edge Zoner finns i tre diskreta erbjudanden:

- Azure Edge-zoner
- Azure Edge-zoner med operatör
- Azure Private Edge-zoner

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge-zoner

![Kantzoner](./media/edge-zones-overview/edge-zones.png "Kantzoner")

Azure Edge Zones är små fotavtryckstillägg för Azure som placeras i befolkningscentra som ligger långt från Azure-regioner. Azure Edge Zones stöder virtuella datorer, behållare och en utvald uppsättning Azure-tjänster som låter dig köra latenskänsliga och dataflödesintensiva program nära slutanvändare. Azure Edge Zones är en del av Microsofts globala nätverk och erbjuder säker, tillförlitlig och hög bandbreddsanslutning mellan program som körs i Edge-zonen nära användaren och den fullständiga uppsättningen Azure-tjänster som körs inom Azure-regioner. Azure Edge Zones ägs och drivs av Microsoft och låter dig använda samma uppsättning Azure-verktyg och portal för att hantera och distribuera tjänster till Edge Zoner.

Typiska användningsfall är:

- Spel- och spelstreaming
- Direktuppspelning av media och leverans av innehåll
- Analys och inferencing i realtid med hjälp av artificiell intelligens och maskininlärning
- Rendering för mixad verklighet

Azure Edge Zones kommer att vara tillgängliga i följande tunnelbanor:

- New York , NY
- Los Angeles , Kalifornien
- Miami, FL

[Kontakta Edge Zones-teamet](https://aka.ms/EdgeZones) för mer information.

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge-zoner med operatör

![Kantzoner med bärare](./media/edge-zones-overview/edge-carrier.png "Kantzoner med bärare")

Azure Edge Zoner med operatör är små fotavtryck tillägg av Azure placeras i mobiloperatörer datacenter i befolkningscentra. Azure Edge Zoner med carrier-infrastruktur placeras ett hopp bort från mobiloperatörens 5G-nätverk, som erbjuder under 10 millisekunder svarstid till program från mobila enheter. Azure Edge Zoner med operatör distribueras i mobiloperatörernas datacenter och är anslutna till Microsofts globala nätverk. De erbjuder säker, tillförlitlig och hög bandbreddsanslutning mellan program som körs nära användaren och den fullständiga uppsättningen Azure-tjänster som körs inom Azure-regioner. Utvecklare kan använda samma uppsättning välbekanta verktyg för att skapa och distribuera tjänster till Edge Zoner.

Typiska användningsfall är:

- Spel- och spelstreaming
- Direktuppspelning av media och leverans av innehåll
- Analys och inferencing i realtid med hjälp av artificiell intelligens och maskininlärning
- Rendering för mixad verklighet
- Anslutna bilar
- Telemedicin

Edge Zones kommer att erbjudas i samarbete med följande operatörer:

- PÅ&T (Atlanta, Dallas och Los Angeles)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge-zoner

![Privata edge-zoner](./media/edge-zones-overview/private-edge.png "Privata edge-zoner")

Azure Private Edge Zones är små fotavtryckstillägg för Azure som placeras lokalt. Azure Private Edge Zones baseras på [Azure Stack Edge-plattformen](https://azure.microsoft.com/products/azure-stack/edge/) och möjliggör låg latensåtkomst till dator- och lagringstjänster som distribueras lokalt. Med Private Edge Zones kan du också distribuera virtualiserade nätverksfunktioner (VNFs) till exempel mobila paketkärnor, routrar, brandväggar och SD-WAN-enheter och program från ISVS som [Azure-hanterade program](https://azure.microsoft.com/services/managed-applications/) sida vid sida tillsammans med virtuella datorer och behållare lokalt. Azure Private Edge Zones levereras med en molnbaserad orkestreringslösning som gör att du kan hantera livscykeln för Virtualized Network Functions (VNF) och program från Azure-portalen.

Med Azure Private Edge Zones kan du utveckla och distribuera program lokalt med samma välbekanta verktyg som används för att skapa och distribuera program i Azure. Du kan också köra privata mobilnät (privat LTE, privat 5G), säkerhetsfunktioner som brandväggar och utöka dina lokala nätverk över flera grenar och Azure med SD-WAN-enheter på samma Private Edge Zone-enheter och hantera dem från Azure.

Typiska användningsfall är:

- Kommandot och kontrollen i realtid inom robotteknik
- Analys och inferencing i realtid med artificiell intelligens och maskininlärning
- Maskinseende
- Fjärrrendering för mixade verklighets- och VDI-scenarier
- Övervakning och säkerhet

Vi har ett rikt ekosystem av VNF-leverantörer, ISV-enheter och MSP-partner för att möjliggöra heltäckande lösningar med private edge-zoner. [Kontakta Private Edge Zones-teamet](https://aka.ms/EdgeZonesPartner) för mer information.

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Privata edge-zoner - Partners

![Privata Edge Zone-partner](./media/edge-zones-overview/partners.png "Privata Edge Zone-partner")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Virtualiserade nätverksfunktioner (VNFs)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Virtualiserad utvecklad paketkärna (vEPC) för mobila nätverk

- [Bekräftade nätverk](https://www.affirmednetworks.com/)
- [Druid Programvara](https://www.druidsoftware.com/)
- [Expeto (expeto)](https://www.expeto.io/)
- [Mavenir (svenska)](https://mavenir.com/)
- [Metaswitch (metaswitch)](https://www.metaswitch.com/)
- [Nokia Digital Automation Moln](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Mobila radiopartners

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN-leverantörer

- [NetFoundry (netto)](https://netfoundry.io/)
- [NuageNetworks från Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN av Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Leverantörer av router

- [Arista](https://www.arista.com/)

[Kontakta Private Edge Zones-teamet](https://aka.ms/EdgeZonesPartner) för mer information om hur du blir partner.

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Leverantörer av brandväggar

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Leverantörer av managed solutions - mobiloperatörer och globala systemintegratörer

| Globala betydande institut och operatörer | Mobiloperatörer |
| --- | --- |
| Amdocs (enheter)                       | Etisalat             |
| Amerikanska tornet               | Rogers               |
| Expeto (expeto)                       | Singtel (singtel)              |
| Federerad trådlös           | Vodafone             |
| Infosys                      |      *                |
| Tech Mahindra                |      *                |

[Kontakta Private Edge Zones-teamet](https://aka.ms/EdgeZonesPartner) för mer information om hur du blir partner.

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Privata edge-zoner - Lösningar

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Privat mobilnät i privata edge-zoner

![Privat mobilnät i privata edge-zoner](./media/edge-zones-overview/mobile-networks.png "Privat mobilnät i privata edge-zoner")

Du kan nu distribuera ett privat mobilnät i privata edge-zoner. Privata mobilnät möjliggör extremt låg latens, hög kapacitet och ett tillförlitligt och säkert trådlöst nätverk som krävs för affärskritiska program. Privata mobilnät kan möjliggöra scenarier som kommando och kontroll av automatiserade guidade fordon (AGV) i ett lager, realtidskommunikation mellan robotar i en smart fabrik och förstärkt realty och virtual reality-edge-applikationer.

Den virtualiserade utvecklade paketkärna (vEPC) nätverksfunktionen utgör hjärnan i ett privat mobilnät. Du kan nu distribuera en vEPC på Private Edge Zones. En lista över vEPC-partner som är tillgängliga i privata edge-zoner finns i [vEPC ISV: er](#vEPC).

Distribuera en privat mobil nätverkslösning på Private Edge Zoner kräver andra komponenter såsom mobila åtkomstpunkter, SIM-kort och andra VPN-filer som routrar. Åtkomst till licensierat eller olicensierat spektrum är avgörande för att skapa ett privat mobilnät. Dessutom kan du behöva hjälp med RF-planering, fysisk layout, installation och support. En lista över partner finns i [Mobila radiopartners](#mobile-radio).

Microsoft tillhandahåller ett partnerekosystem som kan hjälpa till med alla aspekter av den här processen – från planering av nätverket, inköp av nödvändiga enheter, konfiguration av maskinvara till hantering av konfigurationen från Azure. Med en uppsättning validerade partner som är tätt integrerade med Microsoft kan du vara säker på att lösningen kommer att vara tillförlitlig och enkel att använda. Du kan fokusera på dina kärnscenarier, samtidigt som du förlitar dig på att Microsoft och dess partner hjälper till med resten.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN på privata edge-zoner

![SD-WAN på privata edge-zoner](./media/edge-zones-overview/sd-wan.png "SD-WAN på privata edge-zoner")
 
Med SD-WAN som teknik kan du skapa WAN (Wide Area Networks) i företagsklass med ökad bandbredd, högpresterande åtkomst till molnet, tjänsteinfogning, tillförlitlighet, principhantering och omfattande nätverkssynlighet. SD-WAN tillhandahåller sömlös filialkontorsanslutning iscensatt från redundanta centrala styrenheter till lägre ägandekostnad.
Med SD-WAN på Private Edge Zones kan du gå från capex-centrerad modell till en SaaS-modell (Software-as-a-service) för att minska IT-budgetarna. Du kan använda ditt val av SD-WAN-partners orchestrator eller controller för att aktivera nya tjänster och sprida dem i hela nätverket omedelbart.

## <a name="next-steps"></a>Nästa steg

För mer information, kontakta följande team:

* [Team för edgezoner](https://aka.ms/EdgeZones)
* [Private Edge Zones team - att bli partner](https://aka.ms/EdgeZonesPartner)
