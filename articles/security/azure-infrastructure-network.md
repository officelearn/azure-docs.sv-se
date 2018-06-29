---
title: Arkitektur för Azure-nätverk
description: Den här artikeln innehåller en allmän beskrivning av nätverkets infrastruktur för Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102371"
---
# <a name="azure-network-architecture"></a>Arkitektur för Azure-nätverk
Arkitektur för Azure-nätverk följer en modifierad version av industry standard åtkomst-Core/Distribution modellen med distinkta maskinvara lager. Lagren är:

- Core (Datacenter routrar)
- Distribution (åtkomst routrar och aggregering L2) - Distribution lagret avgränsar L3 routning L2 växlar
- Åtkomst (L2 värden växlar)

Nätverksarkitekturen har två nivåer av nivå 2-switchar, ett lager sammanställa trafik från den andra och lager 2 slingor för att tillämpa redundans. Detta ger fördelar som en mer flexibel VLAN storleken och förbättrar port skalning. Arkitekturen håller L2 och nivå 3 distinkt som tillåter användning av maskinvara i varje distinkta lager i nätverket och minimerar antalet fel i ett skikt från att påverka andra lager. Användning av trunkar tillåter resursdelning, till exempel anslutningen till L3-infrastruktur.

## <a name="network-configuration"></a>Nätverkskonfiguration
Nätverksarkitektur i ett kluster för Microsoft Azure inom ett datacenter består av följande enheter:

- Router (Datacenter, åtkomst Router och lägsta gränsroutrar)
- Växlar (aggregering och överkant växlar Rack)
- Digi CMs
- PDU eller Nucleons

Bilden nedan ger en översikt över Azure nätverksarkitektur inom ett kluster. Azure har två separata arkitekturer. Vissa befintliga Azure-kunder och delade tjänster finns på standard-LAN-arkitektur (DLA) medan nya regioner och virtuella kunder ska vara tillgänglig med arkitekturen för Quantum 10 (fråga10). DLA arkitektur är en traditionell trädet design med aktiva och passiva åtkomst routrar och säkerhet ACL: er som används för åtkomst-routrar. Arkitektur för Quantum 10 är en clos/nät design av routrar där ACL: er används inte routrar men under Routning via programvara Load Balancing (SLB) eller programvarudefinierade VLAN.

![Azure-nätverk][1]

### <a name="quantum-10-devices"></a>Quantum 10 enheter
Quantum 10 design en nivå 3 växling sprids över flera enheter i en CLOS/nät-design. Fördelarna med fråga10 design är större funktion och större möjlighet att skala befintliga nätverksinfrastruktur. Designen använder gränsroutrar för löv, angivna växlar och upp av Rack routrar för att skicka trafik till kluster över flera vägar för feltolerans. Säkerhet tjänster såsom nätverksadresser hanteras via belastningsutjämning för programvara i stället för på maskinvaruenheter.

### <a name="access-routers"></a>Åtkomst-routrar
Distribution/access L3 routrar (ARs) utför primära routningsfunktionen för distribution och åtkomst-lager. Dessa enheter distribueras som ett par och är standard-gateway för undernät. Varje par AR kan stödja flera L2 aggregering växeln-par, beroende på kapaciteten. Det maximala antalet beror på kapaciteten för enheter, samt fel domäner. En typisk nummer utifrån förväntade kapacitet är tre L2 aggregering växeln par AR par.

### <a name="l2-aggregation-switches"></a>L2 Aggregering växlar  
Dessa enheter fungerar som en aggregeringspunkt för L2 trafik. De distribution lagret för L2 infrastruktur och kan hantera stora mängder trafik. Eftersom dessa enheter aggregera trafik, 802.1Q-funktioner och tekniker för hög bandbredd, till exempel port aggregering och 10GE används.

### <a name="l2-host-switches"></a>L2 Värden växlar
Värdar som ansluter direkt till växlarna. De kan vara rackmonterade växlar eller chassi distributioner. Den 802.1Q som standard tillåter beteckningen för ett virtuellt lokalt nätverk som ett ursprungligt VLAN behandlar det VLANET som normal (ej taggade) Ethernet-ramar. Under normala omständigheter ramar på ursprungligt VLAN skickas och tas emot utan märkord på en 802.1Q trunk port. Den här funktionen har utformats för migrering till 802.1Q och kompatibilitet med icke-802.1Q kompatibla enheter. I den här arkitekturen använder nätverkets infrastruktur ursprungligt VLAN.

Den här arkitekturen anger en standard för interna VLAN-val som säkerställer att, om möjligt att AR-enheter har en unik ursprungligt VLAN för varje trunk och L2Aggregation till L2Aggregation trunkar. L2Aggregation till L2Host växel trunkar har ett ursprungligt VLAN inte är standard.

### <a name="link-aggregation-8023ad"></a>Link Aggregation (802.3ad)
Link Aggregation (FÖRDRÖJNING) kan flera enskilda länkar till buntas ihop och behandlas som en logisk länk. Numret används för att ange port-kanal gränssnitt måste ställas in så för att göra operativa felsökning resten av nätverket använder samma nummer i båda ändar av en port-kanal. Detta kräver en standard för att avgöra vilka slutet av port-kanalen definiera nästa tillgängliga nummer.

Siffror som angetts för L2Agg till L2Host växel är port-kanal-nummer som används på L2Agg sida. Standarden är att använda tal 1 och 2 på L2Host-sida för att referera till port-kanalen kommer att sidan ”a” och ”b” respektive eftersom intervallet för siffror är mer begränsad på L2Host-sida.

### <a name="vlans"></a>VLAN
Nätverksarkitekturen använder VLAN-nätverk för gruppen servrar tillsammans i en enda broadcast-domän. VLAN-nummer som överensstämmer med 802.1Q standarder som stöder VLAN numrerade 1 – 4094.

### <a name="customer-vlans"></a>Kunden VLAN
Kunder har olika alternativ för implementering av VLAN kan distribuera Azure-portalen så att den passar uppdelning och arkitektur för sina lösningar. Dessa lösningar distribueras via virtuella datorer. Kundreferens arkitektur exemplen finns [Azure referensarkitekturer](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edge-arkitektur
Azure-Datacenter bygger på hög redundant och väl etablerats nätverksinfrastrukturer. Nätverk i Azure-Datacenter implementeras med ”måste plus ett” (N + 1) redundans arkitekturer eller bättre. Fullständig redundans funktionerna inom och mellan Datacenter bidrar till att garantera nätverket och tjänsten. Externt, hanteras Datacenter av dedikerade nätverk med hög bandbredd kretsar som ansluter redundant egenskaper med över 1200 Internetleverantörer globalt på flera peering punkter tillhandahåller mer än 2 000 Gbps potentiell edge-kapacitet över nätverket.

Filtrera routrar i kant och åtkomst-lagret för Microsoft Azure-nätverk är väl etablerad säkerhet på paketnivå att förhindra att obehöriga försök att ansluta till Azure. De hjälper till att säkerställa att det faktiska innehållet i paketen innehåller data i rätt format och överens med schemat för förväntade klient/server-kommunikation. Azure implementerar en skiktad arkitektur som består av följande nätverk särskiljning och access control-komponenter:

- Kant routrar - särskilja Programmiljö från Internet. Routrar i utkanten är utformade för att skydda mot spoof och begränsa åtkomst med hjälp av åtkomstkontrollistor (ACL).
- Distribution (åtkomst) routrar - åtkomst router är utformade för att tillåta endast Microsoft godkända IP-adresser, ger skydd mot förfalskning och upprätta anslutningar med ACL: er.

### <a name="a10-ddos-mitigation-architecture"></a>A10 DDOS minskning arkitektur
DOS-attacker fortsätta att presentera en verkliga hot mot tillförlitlighet för Microsoft online services. Attacker blir mer fokuserad, mer avancerade och som Microsofts tjänster blir mer geografiskt olika möjligheten att erbjuda effektiva mekanismer för att identifiera och minimera effekten av dessa attacker är hög prioritet.

Informationen nedan förklaras hur systemets A10 DDOS minskning implementeras ur nätverket arkitektur.  
Microsoft Azure använder A10 nätverksenheter vid DCR (Datacenter Router) som ger automatisk identifiering och lösning. A10 lösningen använder Azure nätverksövervakning exempel Net flödet paket och avgöra om det finns en attack. När angrepp har identifierats används A10 enheter som Gastvättar säkerhet. Efter migrering av ytterligare tillåts ren trafik till Azure-datacentret direkt från DCR. A10 lösningen används för att skydda Azure nätverkets infrastruktur.

DDoS-skydd i A10 lösningen inkluderar:

- UDP IPv4 och IPv6 översvämma skydd
- ICMP IPv4 och IPv6 översvämma skydd
- TCP IPv4 och IPv6 översvämma skydd
- TCP SYN attack skydd för IPv4 och IPv6
- Fragmentering attack

> [!NOTE]
> DDoS-skydd är som standard för alla Azure-kunder.
>
>

## <a name="network-connection-rules"></a>Anslutningen Nätverksregler
Azure distribuerar edge routrar i nätverket som tillhandahåller säkerhet på paketnivå att förhindra att obehöriga försök att ansluta till Microsoft Azure. De se till att det faktiska innehållet i paketen innehåller data i rätt format och överens med schemat för förväntade klient/server-kommunikation.

Routrar i utkanten särskilja Programmiljö från Internet. Routrar i utkanten är utformade för att skydda mot spoof och begränsa åtkomst med hjälp av åtkomstkontrollistor (ACL). Dessa routrar i utkanten konfigureras med en riskbedömning ACL till gränsen nätverksprotokoll som tillåts att transit edge-routrar och komma åt routrar.

Nätverksenheter är placerade på åtkomst och kanten platser och fungera som gräns punkter där ingång och utgång filtren.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
