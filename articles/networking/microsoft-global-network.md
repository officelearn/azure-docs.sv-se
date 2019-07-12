---
title: Microsoft global network - Azure
description: Beskriver hur Microsoft skapar dess snabb och tillförlitlig globalt nätverk
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: ypitsch,kumud
ms.openlocfilehash: bcd2415255643926db2cd4e1e4e55bb7381d6437
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672539"
---
# <a name="microsoft-global-network"></a>Microsofts globala nätverk

Microsoft äger och fungerar något av de största stamnät nätverken i hela världen. Den här arkitekturen globala och avancerade utsträckning mer än 100 000 mil och ansluter våra datacenter och kunder. 
 
Varje dag, kunder över hela världen ansluta och skicka biljoner begäranden till Microsoft Azure, Bing, Dynamics 365, Office 365, XBox och mycket annat. Oavsett typ kräver kunder omedelbar tillförlitlighet och tillgänglighet från våra tjänster. 
 
Den [Microsofts globala nätverk](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) är en central del av leverera en utmärkt cloud-upplevelse. Ansluta våra Microsoft [Datacenter](https://azure.microsoft.com/global-infrastructure/) vårt globala nätverk över 54 Azure-regioner och stora nät av kantnoder strategiskt placerade över hela världen, erbjuder både tillgängligheten, kapacitet och flexibilitet för att uppfylla alla behov.

![Microsofts globala nätverk](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Hämta nätverket i premium-molnet
 
Väljer den [bästa möjliga upplevelse](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) är enkelt att när du använder Microsoft-molnet. Från det ögonblick då när kundtrafiken kommer vårt globala nätverk via vår strategiskt kantnoder, skickas dina data genom optimerad routning på nära ljus hastighet. Detta säkerställer optimala svarstider för bästa prestanda. Edge-noderna, alla samman med mer än 3500 unika Internet-partners (peer-datorer) via tusentals anslutningar på mer än 145 platser ger grunden för vår sammanlänkning strategi. 
 
Om du ansluter från London till Tokyo, eller från Washington DC till Los Angeles, är nätverkets prestanda kvantifierade och påverkas av saker, till exempel svarstider, jitter, paketförlust och dataflöde.  På Microsoft vi föredrar och Använd direkta anslutningar till skillnad från överföring länkar kan detta håller svarstrafik symmetriska och hjälper dig att hålla hopp, peering parter och sökvägar som kort och enkelt som möjligt. 

Till exempel om en användare i London försöker få åtkomst till en tjänst i Tokyo, och sedan Internettrafik anger ett av våra kanter i London, färdas över Microsoft WAN via Frankrike, våra gjordes Trans bedömningen sökvägar mellan Europa och Indien och sedan till Japan där tjänsten finns. Svarstrafik är symmetriska. Detta kallas ibland som [kall ljus routning](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) vilket innebär att trafiken stannar i Microsoft network så länge som möjligt innan vi lämnar den.  
  
Så, betyder det alla trafik när du använder Microsoft-tjänster? Ja, Datacenter trafik mellan data i Microsoft Azure eller mellan Microsoft-tjänster som virtuella datorer, Office 365, XBox, SQL-databaser, lagring och virtuella nätverk dirigeras inom vårt globala nätverk och aldrig via det offentliga Internet, att säkerställa optimal prestanda och integritet.  
 
Stora investeringar i fiber kapacitet och mångfald mellan appar i metro, land och ubåt sökvägar är avgörande för oss att hålla konsekvent och hög servicenivå vid bränsle åt extrem tillväxten av våra cloud och online-tjänster. Senaste tillägg till vårt globala nätverk är vår [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) Undervattenskabel, branschens första öppna rad System (OLS) över subsea mellan Bilbao, Spanien och Virginia stranden, Virginia, USA, samt de [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1)mellan New York, USA och Dublin, Irland och [mellan nya och Stillahavsområdet (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) mellan Tokyo, Japan och Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Vårt nätverk är ditt nätverk

Vi har gjort två lång erfarenhet av, tillsammans med stora investeringar i nätverket, för att säkerställa optimala prestanda hela tiden. Företag kan dra full nytta av våra nätverk tillgångar och skapa avancerade överlägget arkitektur längst upp. 
 
Microsoft Azure erbjuder den bästa möjliga portföljen med tjänster och funktioner, så att kunder snabbt och enkelt bygga, utöka och Uppfyll nätverkskraven var som helst. Vår övriga familj av tjänster sträcker sig över vnet-peering mellan regioner, hybrid och i molnet punkt-till-plats och arkitekturer för plats-till-plats samt globala IP överföra scenarier.  För företag som vill ansluta sina egna datacenter eller nätverk till Azure eller kunder med stora datainmatning eller överföring behov [ExpressRoute](../expressroute/expressroute-introduction.md), och [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) tillhandahåller alternativ för upp till 100 Gbit/s bandbredd direkt till Microsofts globala nätverk på peering-platser över hela världen.  
 
[ExpressRoute Global räckvidd](../expressroute/expressroute-global-reach.md) är avsedd att komplettera din tjänstleverantör WAN implementering och ansluta din lokala platser över hela världen. Om du kör en global åtgärd, kan du använda ExpressRoute Global räckvidd tillsammans med dina önskade och lokala tjänstleverantörer för att ansluta alla globala platser med hjälp av Microsofts globala nätverk. Expandera ditt nya nätverk i molnet (WAN) för att innefatta stora mängder filialkontoren kan utföras via Azure virtuellt WAN-nätverk, vilket ger dig möjlighet att sömlöst ansluta dina grenar till Microsofts globala nätverk med SDWAN & VPN-enheter (dvs. Kunden lokal utrustning eller CPE) med inbyggda och enkel att använda automatisk hantering av anslutningen och konfigurationen. 
 
[Global VNet-peering](../virtual-network/virtual-network-peering-overview.md) gör att kunderna kan smidigt ansluta två eller flera Azure-nätverk i olika regioner. När peer-kopplats visas de virtuella nätverken som en. Trafiken mellan virtuella datorer i peerkopplade virtuella nätverk dirigeras via Microsoft-stamnätsinfrastrukturen, nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk - via privata IP-adresser endast. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Väl hanteras med hjälp av programdefinierade innovation

Kör något av de ledande molnen i världen, har Microsoft fått mycket insikter och erfarenhet av [att skapa och hantera](https://myignite.techcommunity.microsoft.com/sessions/66668) global infrastruktur med höga prestanda.  
 
Vi följer en stabil uppsättning operativa principer: 
 
- Använda bästa möjliga byta maskinvara på de olika nivåerna av nätverket.  
- Distribuera nya funktioner utan påverkan för slutanvändare.  
- Distribuera uppdateringar säkert och tillförlitligt hela flottan, så snabbt som möjligt. Timmar i stället för veckor.  
- Använda djup telemetri i molnskala och helt automatiserade fault-lösning.  
- Använd teknik för enhetlig och programvarudefinierade nätverk för att styra alla maskinvaru-element i nätverket.  Ta bort duplicering och minska fel. 
 
Dessa principer gäller för alla lager i nätverket: från värden nätverksgränssnitt, byta plattform nätverks-funktioner i datacenter, till exempel belastningsutjämnare, ända upp till WAN med våra tekniska trafik-plattformen och våra optiska nätverk.  
 
Den exponentiella tillväxten av Azure och dess nätverk har nått en punkt där vi så småningom insåg att mänskliga intuition kunde inte längre förlita sig på att hantera de globala nätverksåtgärderna. Vi utvecklat en plattform för att spegla och emulera vår produktionsnätverk syntetiskt för att uppfylla behovet av att verifiera långa, medel och kortsiktiga ändringar i nätverket. Möjlighet att skapa speglade miljöer och köra flera miljoner simuleringar, kan vi testa programvara och ändring av maskinvara och hur påverkar innan du arrangerar en vår plattform för produktion och nätverk. 

## <a name="next-steps"></a>Nästa steg
- [Mer information om nätverkstjänsterna i Azure](https://azure.microsoft.com/product-categories/networking/)
