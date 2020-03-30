---
title: Azure-nätverksarkitektur
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-infrastrukturnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727180"
---
# <a name="azure-network-architecture"></a>Azure-nätverksarkitektur
Azure-nätverksarkitekturen följer en modifierad version av branschstandardens kärn-/distributions-/åtkomstmodell med olika maskinvarulager. Lagren inkluderar:

- Kärna (datacenterroutrar)
- Distribution (åtkomstroutrar och L2-aggregering). Distributionslagret separerar L3-routning från L2-omkoppling.
- Åtkomst (L2-värdväxlar)

Nätverksarkitekturen har två nivåer av lager 2-växlar. Ett lager sammanställer trafik från det andra lagret. Det andra lagret loopar för att införliva redundans. Arkitekturen ger ett mer flexibelt VLAN-fotavtryck och förbättrar portskalningen. Arkitekturen håller L2 och L3 distinkta, vilket gör att användningen av hårdvara i vart och ett av de distinkta lagren i nätverket, och minimerar fel i ett lager från att påverka det andra lagret(erna). Användningen av trunkar möjliggör resursdelning, till exempel anslutning till L3-infrastrukturen.

## <a name="network-configuration"></a>Nätverkskonfiguration
Nätverksarkitekturen för ett Azure-kluster i ett datacenter består av följande enheter:

- Routrar (datacenter, åtkomstroutrar och routrar för kantblad)
- Växlar (aggregering och top-of-rack-switchar)
- Digi CMs
- Kraftdistributionsenheter

Azure har två separata arkitekturer. Vissa befintliga Azure-kunder och delade tjänster finns på standard-LAN-arkitekturen (DLA), medan nya regioner och virtuella kunder finns på Arkitekturen Quantum 10 (Q10). DLA-arkitekturen är en traditionell träddesign, med aktiva/passiva åtkomstroutrar och åtkomstkontrollistor (ACL) som tillämpas på åtkomstroutrarna. Quantum 10-arkitekturen är en Close/mesh-design av routrar, där ACL inte tillämpas på routrarna. I stället tillämpas ACL:er under routningen, via Software Load Balancing (SLB) eller programvarudefinierade VLAN.Instead, ACS are applied below the routing, through Software Load Balancing (SLB) or software defined VLAN.

Följande diagram ger en översikt på hög nivå över nätverksarkitekturen i ett Azure-kluster:

![Diagram över Azure-nätverk](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10-enheter
Quantum 10-designen utför lager 3-växling spridda över flera enheter i en Clos/mesh-design. Fördelarna med Q10-designen inkluderar större kapacitet och större förmåga att skala befintlig nätverksinfrastruktur. Designen använder routrar för gränsblad, ryggradsväxlar och rackroutrar för att skicka trafik till kluster över flera vägar, vilket möjliggör feltolerans. Belastningsutjämning av programvara, i stället för maskinvaruenheter, hanterar säkerhetstjänster som översättning av nätverksadresser.

### <a name="access-routers"></a>Komma åt routrar
Distributions-/åtkomst-L3-routrarna (ARs) utför de primära routningsfunktionerna för distributions- och åtkomstlager. Dessa enheter distribueras som ett par och är standardgateway för undernät. Varje AR-par kan stödja flera L2-aggregeringsbrytarepar, beroende på kapacitet. Det maximala antalet beror på enhetens kapacitet och feldomäner. Ett typiskt tal är tre L2-aggregeringsbrytarepar per AR-par.

### <a name="l2-aggregation-switches"></a>L2 aggregering växlar  
Dessa enheter fungerar som en aggregeringspunkt för L2-trafik. De är distributionslagret för L2-tyget och kan hantera stora mängder trafik. Eftersom dessa enheter aggregerade trafik kräver de 802.1q-funktioner och teknik med hög bandbredd, till exempel portaggregering och 10GE.

### <a name="l2-host-switches"></a>L2-värdväxlar
Värdar ansluter direkt till dessa växlar. De kan vara rackmonterade brytare eller chassidistributioner. Den 802.1q standarden möjliggör beteckningen av en VLAN som en infödd VLAN, behandla det VLAN som vanligt (otaggat) Ethernet inramning. Under normala omständigheter överförs och tas ramar på den inbyggda VLAN över och tas emot otaggade på en 802,1q trunk port. Den här funktionen har utformats för migrering till 802.1q och kompatibilitet med enheter som inte är 802,1q. I den här arkitekturen är det bara nätverksinfrastrukturen som använder den inbyggda VLAN.In this architecture, only the network infrastructure uses the native VLAN.

Den här arkitekturen anger en standard för inbyggt VLAN-val. Standarden säkerställer, där så är möjligt, att AR-enheterna har en unik, infödd VLAN för varje trunk och L2Aggregation till L2Aggregation trunkar. L2Aggregation till L2Host Switch-trunkarna har en inbyggd VLAN som inte är standard.

### <a name="link-aggregation-8023ad"></a>Länk aggregering (802.3ad)
Länkaggregering gör att flera enskilda länkar kan buntas ihop och behandlas som en enda logisk länk. För att underlätta driftfelsökning bör det nummer som används för att ange portkanalgränssnitt standardiseras. Resten av nätverket använder samma nummer i båda ändar av en portkanal.

De nummer som anges för L2Agg till L2Host-växeln är portkanalnumren som används på L2Agg-sidan. Eftersom intervallet av siffror är mer begränsad på L2Host sidan, är standarden att använda nummer 1 och 2 på L2Host sidan. Dessa hänvisar till port-kanal går till "a" sida och "b" sida, respektive.

### <a name="vlans"></a>Vlan
Nätverksarkitekturen använder VLAN för att gruppera servrar tillsammans till en enda broadcast-domän. VLAN-nummer överensstämmer med 802.1q standard, som stöder VLAN numrerade 1-4094.

### <a name="customer-vlans"></a>Kund-VPN
Du har olika VLAN-implementeringsalternativ som du kan distribuera via Azure-portalen för att uppfylla lösningens separations- och arkitekturbehov. Du distribuerar dessa lösningar via virtuella datorer. Exempel på kundreferensarkitektur finns i [Azure-referensarkitekturer](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edge-arkitektur
Azure-datacenter bygger på mycket redundanta och väletablerade nätverksinfrastrukturer. Microsoft implementerar nätverk inom Azure-datacenter med "need plus one" (N+1) redundansarkitekturer eller bättre. Fullständiga redundansfunktioner inom och mellan datacenter bidrar till att säkerställa nätverks- och tjänsttillgänglighet. Externt betjänas datacenter av dedikerade nätverkskretsar med hög bandbredd. Dessa kretsar ansluter redundant egenskaper med över 1200 internetleverantörer globalt vid flera peering-punkter. Detta ger över 2 000 Gbit/s potentiell kantkapacitet i nätverket.

Filtrering av routrar vid kanten och åtkomstskiktet i Azure-nätverket ger väletablerad säkerhet på paketnivå och hjälper till att förhindra obehöriga försök att ansluta till Azure. Routrarna hjälper till att säkerställa att det faktiska innehållet i paketen innehåller data i det förväntade formatet och överensstämmer med det förväntade kommunikationsschemat för klient/server. Azure implementerar en nivåindelade arkitektur som består av följande nätverkssegregering och åtkomstkontrollkomponenter:

- **Edge-routrar.** Dessa segregera applikationsmiljön från Internet. Edge-routrar är utformade för att ge skydd mot förfalskning och begränsa åtkomst med hjälp av ACL:er.
- **Distributionsroutrar (åtkomst).** Dessa tillåter endast Microsoft-godkända IP-adresser, tillhandahåller anti-förfalskning och upprättar anslutningar med hjälp av ACL:er.

### <a name="ddos-mitigation"></a>DDOS-begränsning
DDoS-attacker (Distributed Denial of Service) fortsätter att utgöra ett verkligt hot mot onlinetjänsternas tillförlitlighet. I takt med att attacker blir mer riktade och sofistikerade, och eftersom de tjänster som Microsoft tillhandahåller blir mer geografiskt diversifierade, är det hög prioritet att identifiera och minimera effekterna av dessa attacker.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) skyddar mot DDoS-attacker. Mer information finns i [Azure DDoS Protection: Metodtips och referensarkitekturer](ddos-best-practices.md) för att lära dig mer.

> [!NOTE]
> Microsoft tillhandahåller DDoS-skydd som standard för alla Azure-kunder.
>
>

## <a name="network-connection-rules"></a>Regler för nätverksanslutning
I nätverket distribuerar Azure kantroutrar som ger säkerhet på paketnivå för att förhindra obehöriga försök att ansluta till Azure. Edge-routrar säkerställer att det faktiska innehållet i paketen innehåller data i det förväntade formatet och överensstämmer med det förväntade klient-serverkommunikationsschemat.

Edge routrar segregera applikationsmiljön från Internet. Dessa routrar är utformade för att ge skydd mot förfalskning och begränsa åtkomsten med hjälp av ACL:er. Microsoft konfigurerar kantroutrar med hjälp av en nivåindelad ACL-metod för att begränsa nätverksprotokoll som tillåts att transitera kantroutrar och komma åt routrar.

Microsoft placerar nätverksenheter på åtkomst- och kantplatser för att fungera som gränspunkter där ingående eller utgående filter används.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)


