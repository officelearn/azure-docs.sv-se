---
title: Arkitektur för Azure-nätverk
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-nätverkets infrastruktur.
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
ms.openlocfilehash: a6800b18d1bb588c747d4e9ef7049ac4cbb82f60
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213482"
---
# <a name="azure-network-architecture"></a>Arkitektur för Azure-nätverk
Arkitektur för Azure-nätverk följer en modifierad version av branschens standard core/distribution/access modellen, med olika maskinvara lager. Lager är:

- Core (datacenter routrar)
- Distribution (åtkomst routrar och L2 aggregering). Distribution lagret separerar L3 routning L2 växlar.
- Åtkomst (L2 värden växlar)

Nätverksarkitekturen har två nivåer av layer 2-växlar. Ett lager aggregerar trafik från det andra lagret. Det andra lagret loopar för att införliva redundans. Detta ger en mer flexibel VLAN-fotavtryck och förbättrar port skalning. Arkitekturen ser L2 och L3 distinkta, som tillåter användning av maskinvara i var och en av de olika lager i nätverket och minimerar fel i ett lager påverkar andra lager. Användning av trunkar möjliggör resursdelning, till exempel anslutningen till L3-infrastruktur.

## <a name="network-configuration"></a>Nätverkskonfiguration
Nätverksarkitektur i ett Azure-kluster i ett datacenter består av följande enheter:

- Router (datacenter, åtkomst router och kantlinje löv routrar)
- Växlar (aggregering och Tor växlar)
- Digi CMs
- Kraftfördelningsenheter

Azure har två separata arkitekturer. Vissa befintliga Azure-kunder och delade tjänster finns på standard LAN-högnivåarkitektur (DLA), medan nya regioner och virtuella kunder finns på arkitektur för Quantum 10 (fråga10). DLA-arkitekturen är en traditionell trädet design, med aktiv/passiv åtkomst routrar och security åtkomstkontrollistor (ACL) tillämpas på åtkomst-routrar. Quantum-10-arkitekturen är ett Clos/nät utformningen av routrar, där ACL: er inte kan du använda routrar. I stället, tillämpas ACL: er nedan routning, via Software Load Balancing (SLB) eller programvarudefinierade VLAN.

Följande diagram ger en översikt över nätverksarkitekturen i ett Azure-kluster:

![Diagram över Azure-nätverk][1]

### <a name="quantum-10-devices"></a>Quantum-10-enheter
Design för Quantum 10 genomför layer 3 switching spridning över flera enheter i en Clos/nät design. Fördelarna med fråga10 design är större funktions- och större möjlighet att skala befintliga nätverksinfrastrukturen. Designen använder kantlinje löv routrar, växlar stamnät och Tor routrar för att skicka trafik till kluster över flera vägar, vilket ger feltolerans. Belastningsutjämning för programvara, i stället för maskinvaruenheter, hanterar säkerhetstjänster som nätverksadresser.

### <a name="access-routers"></a>Åtkomst-routrar
Distribution/access L3-routrar (ARs) utför primära routningsfunktionen för distribution och åtkomst-lager. Dessa enheter distribueras som ett par och är standard-gateway för undernät. Varje par AR har stöd för flera L2 aggregering switch-par, beroende på kapaciteten. Det maximala antalet beror på kapaciteten för enheten, samt fel domäner. Ett typiskt tal är tre L2 aggregering växel par AR par.

### <a name="l2-aggregation-switches"></a>L2 aggregering växlar  
Dessa enheter fungerar som en aggregeringspunkt för L2 trafik. De är distribution lagret för L2-infrastrukturen och kan hantera stora mängder trafik. Eftersom dessa enheter aggregeras trafik, de kräver 802.1Q funktioner och hög bandbredd tekniker, till exempel port aggregering och 10GE.

### <a name="l2-host-switches"></a>L2 värden växlar
Värdar ansluta direkt till växlarna. De kan vara rackmonterade växlar eller chassi distributioner. Den 802.1Q standard kan utses av ett virtuellt lokalt nätverk till ett ursprungligt VLAN behandlar detta VLAN som normala (ej taggade) Ethernet-synkroniseringstecken. Under normala omständigheter bildrutor på ursprungligt VLAN överförda och mottagna ej taggade på en 802.1Q segmentport. Den här funktionen har utformats för migrering till 802.1Q och kompatibilitet med icke-802.1Q kompatibla enheter. I den här arkitekturen använder endast nätverksinfrastrukturen ursprungligt VLAN.

Den här arkitekturen anger en standard för val av interna VLAN. Standard säkerställer, där det är möjligt, att enheterna som AR har ett unikt, egna VLAN för varje segment och L2Aggregation till L2Aggregation trunkar. L2Aggregation till L2Host växel trunkar har ett ursprungligt VLAN inte är standard.

### <a name="link-aggregation-8023ad"></a>Link aggregation (802.3ad)
Länkaggregering kan flera enskilda länkar buntas ihop och behandlas som en logisk länk. För att underlätta felsökning av operativa, bör en standardiserad numret används för att ange port-kanal gränssnitt. Resten av nätverket använder samma nummer i båda ändar av en port-kanal.

Siffrorna som angetts för L2Agg till L2Host växel är port kanal-värden som används på L2Agg sida. Eftersom en uppsättning siffror är mer begränsad på L2Host sida, är standard att använda tal 1 och 2 på L2Host sida. Dessa avser den port-kanal-gå till sidan ”a” och ”b”-sida respektive.

### <a name="vlans"></a>VLAN
Nätverksarkitekturen använder VLAN-nätverk kan gruppera servrar tillsammans i en enda broadcast-domän. VLAN-nummer överensstämmer med 802.1Q standarder som stöder VLAN numrerade 1 – 4094.

### <a name="customer-vlans"></a>Kund-VLAN
Har du olika alternativ för implementering av VLAN kan du distribuera via Azure portal så att den passar separation och arkitektur för din lösning. Du kan distribuera dessa lösningar via virtuella datorer. Kunden referens arkitektur exempel finns i [Azure-referensarkitekturer](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edge-arkitektur
Azure-datacenter som bygger på mycket redundant och väl etablerade nätverksinfrastrukturer. Microsoft implementerar nätverk i Azure-datacenter med ”behöver plus ett” (N + 1) redundans arkitekturer eller bättre. Funktioner för fullständig redundans inom och mellan Datacenter bidra till att säkerställa nätverks- och tillgänglighet. Externt, betjänas Datacenter av dedikerade nätverk med hög bandbredd kretsar. Dessa kretsar träffa redundant över 1200 Internetleverantörer globalt på flera platser för peering egenskaper. Detta ger som överstiger 2 000 Gbit/s potentiella edge-kapacitet i nätverket.

Filtrerande routrar på edge- och åtkomst för Azure-nätverket tillhandahålla välkänt säkerhet på paketnivå. Detta bidrar till att förhindra obehöriga försök att ansluta till Azure. Routrarna att se till att det faktiska innehållet i paketen innehåller data i det förväntade formatet och överensstämmer med det förväntade klient/server-kommunikation schemat. Azure implementerar en nivåindelad arkitektur, som består av följande nätverksuppdelning och komponenter för kontroll av åtkomst:

- **Edge-routrar.** Dessa särskilja programmiljön från internet. Edge-routrar är utformade för att skydda mot spoof och begränsa åtkomst med hjälp av ACL: er.
- **Distribution (åtkomst) routrar.** Dessa kan bara Microsoft godkända IP-adresser, ger skydd mot förfalskning och upprätta anslutningar med hjälp av ACL: er.

### <a name="a10-ddos-mitigation-architecture"></a>Arkitektur för a10 DDOS-minskning
DOS-attacker fortsätta att visa ett verkligt hot för tillförlitligheten för onlinetjänster. Eftersom attacker blir mer riktade och avancerade och som tjänsterna tillhandahåller Microsoft bli mer geografiskt skilda, identifiera och minimera är effekten av dessa attacker en hög prioritet. Informationen nedan förklaras hur A10 DDOS minskning system har implementerats från ett nätverksperspektiv arkitektur.

Azure använder A10 nätverksenheter vid datacenter-router (DCR) som tillhandahåller automatisk identifiering och skyddsteknik. A10-lösningen använder Azure Network Monitor till exempel flow paket och avgöra om det finns ett angrepp. Om attacken upptäcks Skrubba A10 enheter för att minska attacker. Endast sedan är rensad trafik tillåts i Azure-datacentret direkt från DCR. Microsoft använder A10-lösningen för att skydda Azure nätverkets infrastruktur.

DDoS-skydd i A10-lösningen är:

- UDP IPv4 och IPv6 översvämma skydd
- ICMP IPv4 och IPv6 översvämma skydd
- TCP IPv4 och IPv6 översvämma skydd
- TCP SYN attack skydd för IPv4 och IPv6
- Fragmentering attack

> [!NOTE]
> Microsoft tillhandahåller DDoS-skydd som standard för alla Azure-kunder.
>
>

## <a name="network-connection-rules"></a>Regler för anslutning
Azure distribuerar i nätverket, edge-routrar som tillhandahåller säkerhet på paketnivå att förhindra obehöriga försök att ansluta till Azure. Edge-routrar Kontrollera att det faktiska innehållet i paketen innehåller data i det förväntade formatet och överensstämmer med det förväntade klient/server-kommunikation schemat.

Edge-routrar särskilja programmiljön från internet. Dessa routrar är utformade för att skydda mot spoof och begränsa åtkomsten med hjälp av ACL: er. Microsoft konfigurerar edge-routrar med hjälp av en nivåindelad ACL-metod för gränsen nätverksprotokoll som tillåts att överföra edge-routrar och komma åt routrar.

Microsoft placerar nätverksenheter på åtkomst och edge platser så att den fungerar som gräns punkter där inkommande eller utgående filter tillämpas.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
