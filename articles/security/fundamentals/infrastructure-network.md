---
title: Azure nätverks arkitektur
description: Den här artikeln innehåller en allmän beskrivning av det Microsoft Azure infrastruktur nätverket.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727180"
---
# <a name="azure-network-architecture"></a>Azure nätverks arkitektur
Azures nätverks arkitektur följer en modifierad version av bransch standard Core/distribution/åtkomst modell, med distinkta maskin varu lager. Lagren är:

- Core (datacenter-routrar)
- Distribution (åtkomst till routrar och L2-agg regering). Distributions lagret separerar L3-routning från L2-växling.
- Åtkomst (L2-värd växlar)

Nätverks arkitekturen har två nivåer av Layer 2-växlar. Ett lager samlar in trafik från det andra skiktet. Det andra skiktet upprepas för att införliva redundans. Arkitekturen ger en mer flexibel VLAN-storlek och förbättrar port skalningen. Arkitekturen ser till att L2 och L3 är distinkta, vilket gör att maskin varan kan användas i var och en av de olika skikten i nätverket och minimerar fel i ett lager från att påverka de andra skikten. Användningen av trunkar möjliggör resurs delning, till exempel anslutningen till L3-infrastrukturen.

## <a name="network-configuration"></a>Konfiguration av nätverk
Nätverks arkitekturen i ett Azure-kluster i ett Data Center består av följande enheter:

- Routrar (Data Center, åtkomst till router och gräns löv routrar)
- Växlar (agg regerings-och Top-of-rack-växlar)
- Digi CMs
- Enheter för energi distribution

Azure har två separata arkitekturer. Vissa befintliga Azure-kunder och delade tjänster finns på Standard-LAN-arkitekturen (DLA), medan nya regioner och virtuella kunder finns på Q10-arkitekturen (Quantum 10). DLA-arkitekturen är en traditionell träd design med aktiva/passiva åtkomst-routrar och säkerhets åtkomst kontrol listor (ACL: er) som tillämpas på åtkomst till routrarna. Den Quantum 10-arkitekturen är en stängnings-och nät design för routrar, där ACL: er inte tillämpas på routrarna. I stället tillämpas ACL: er under routningen, genom program varu belastnings utjämning (SLB) eller definierade VLAN-nätverk.

Följande diagram ger en övergripande översikt över nätverks arkitekturen i ett Azure-kluster:

![Diagram över Azure-nätverk](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10-enheter
Designen i Quantum 10 gör att Layer 3 växlar över flera enheter i en Clos/nät-design. Fördelarna med Q10-designen är större kapacitet och större möjligheter att skala befintlig nätverks infrastruktur. Designen använder gräns löv routrar, rygg växlar och högrackbaserade routrar för att skicka trafik till kluster över flera vägar, vilket ger fel tolerans. Belastnings utjämning för program vara, i stället för maskin varu enheter, hanterar säkerhets tjänster som Network Address Translation.

### <a name="access-routers"></a>Åtkomst till routrar
Distributions-/åtkomst-L3-routrarna (ARs) utför den primära Routningsprincipen för distributions-och åtkomst skikten. Dessa enheter distribueras som ett par och är standard-gatewayen för undernät. Varje AR-par har stöd för flera L2-växel par, beroende på kapacitet. Det maximala antalet beror på enhetens kapacitet, samt fel domäner. Ett typiskt nummer är tre L2-växel par per AR-par.

### <a name="l2-aggregation-switches"></a>L2-växlar  
Dessa enheter fungerar som en agg regerings punkt för L2-trafik. De är distributions skiktet för L2-infrastrukturen och kan hantera stora mängder trafik. Eftersom de här enheterna sammanställer trafik, kräver de 802.1-q-funktioner och teknik med hög bandbredd, till exempel port agg regering och 10GE.

### <a name="l2-host-switches"></a>Byten av L2-värden
Värdar ansluter direkt till dessa växlar. De kan vara rackmonterade växlar eller chassi distributioner. 802.1 q-standarden gör det möjligt att utse ett VLAN som ett ursprungligt VLAN-nätverk, vilket behandlar detta VLAN som normal (otaggade) Ethernet-ram. I normala fall skickas ramar i det ursprungliga VLAN-nätverket och tas emot utan märkord i en 802.1 q segment port. Den här funktionen har utformats för migrering till 802.1-q och kompatibilitet med icke-802.1 q-kompatibla enheter. I den här arkitekturen använder endast nätverks infrastrukturen det inbyggda VLAN-nätverket.

Den här arkitekturen anger en standard för val av ursprungligt VLAN. Standarden säkerställer, där det är möjligt, att enheterna har ett unikt, ursprungligt VLAN för varje trunk och L2Aggregation till L2Aggregation trunkar. L2Aggregation till L2Host växlar trunkar har ett ursprungligt virtuellt lokalt nätverk som inte är standard.

### <a name="link-aggregation-8023ad"></a>Länk agg regering (802.3 AD)
Med länk agg regering kan flera enskilda länkar paketeras tillsammans och behandlas som en enda logisk länk. För att under lätta drift fel sökningen bör det tal som används för att ange port kanals gränssnitt vara standardiserat. Resten av nätverket använder samma nummer i båda ändar av en port-kanal.

De tal som anges för växeln L2Agg till L2Host är de Port kanal nummer som används på L2Agg-sidan. Eftersom tal intervallet är mer begränsat på L2Host-sidan är standard att använda nummer 1 och 2 på L2Host-sidan. Dessa hänvisar till port-kanalen på sidan "a" och "b".

### <a name="vlans"></a>VLAN
Nätverks arkitekturen använder VLAN för att gruppera servrar i en enda broadcast-domän. VLAN-nummer stämmer överens med 802.1 q standard, som stöder VLAN som är numrerade 1 – 4094.

### <a name="customer-vlans"></a>Kund-VLAN
Du har olika VLAN-implementerings alternativ som du kan distribuera genom Azure Portal för att uppfylla lösningens separations-och arkitektur behov. Du distribuerar dessa lösningar via virtuella datorer. Exempel på kund referens arkitektur finns i [referens arkitekturer för Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Kant arkitektur
Azure-datacenter bygger på hög redundant och väletablerad nätverks infrastruktur. Microsoft implementerar nätverk i Azure-datacenter med "behöver plus ett" (N + 1) redundans-arkitekturer eller bättre. Fullständiga funktioner för redundans inom och mellan data Center hjälper till att säkerställa nätverks-och tjänst tillgänglighet. Externt betjänas Data Center av dedikerade nätverks kretsar med hög bandbredd. Dessa kretsar redundant ansluter egenskaper med över 1200 Internet leverantörer globalt på flera peering-platser. Detta ger större än 2 000 Gbit/s av potentiell gräns kapacitet i nätverket.

Att filtrera routrar i gräns-och åtkomst skiktet i Azure-nätverket ger väletablerad säkerhet på paket nivån och hjälper till att förhindra obehöriga försök att ansluta till Azure. Routrarna bidrar till att säkerställa att det faktiska innehållet i paketen innehåller data i det förväntade formatet, och att de överensstämmer med det förväntade kommunikations schemat klient/server. Azure implementerar en nivå arkitektur som består av följande komponenter för nätverks särskiljning och åtkomst kontroll:

- **Edge-routrar.** Dessa särskiljer program miljön från Internet. Edge-routrar är utformade för att ge skydd mot förfalskning och begränsa åtkomsten med hjälp av ACL: er.
- **Distributions-routrar (Access).** Dessa tillåter endast Microsoft-godkända IP-adresser, ger skydd mot förfalskning och upprättar anslutningar med hjälp av ACL: er.

### <a name="ddos-mitigation"></a>DDOS-minskning
DDoS-attacker (distributed denial of Service) fortsätter att presentera ett verkligt hot mot tillförlitligheten för onlinetjänster. När attacker blir mer riktade och avancerade, och tjänsterna som Microsoft tillhandahåller blir mer geografiskt skilda, identifierar och minimerar effekten av dessa attacker är en hög prioritet.

[Azure DDoS Protection standard](../../virtual-network/ddos-protection-overview.md) ger skydd mot DDoS-attacker. Se [Azure DDoS Protection: metod tips och referens arkitekturer](ddos-best-practices.md) för mer information.

> [!NOTE]
> Microsoft tillhandahåller DDoS-skydd som standard för alla Azure-kunder.
>
>

## <a name="network-connection-rules"></a>Regler för nätverks anslutning
I sitt nätverk distribuerar Azure yttre routrar som tillhandahåller säkerhet på paket nivå för att förhindra att obehöriga försök att ansluta till Azure. Edge-routrar ser till att det faktiska innehållet i paketen innehåller data i det förväntade formatet, och att de överensstämmer med det förväntade kommunikations schemat klient/server.

Edge-routrarna särskiljer program miljön från Internet. Dessa routrar är utformade för att ge skydd mot förfalskning och begränsa åtkomsten med hjälp av ACL: er. Microsoft konfigurerar Edge-routrar med hjälp av en nivå med ACL-metod, för att begränsa nätverks protokoll som tillåts att överföra yttre routrar och åtkomst till routrar.

Microsoft placerar nätverks enheter på åtkomst-och gräns platser för att fungera som gräns punkter där ingångs-eller utgångs filter tillämpas.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)


