---
title: Azure-produktionsnätverket
description: Den här artikeln innehåller en allmän beskrivning av Microsoft Azure-produktionsnätverket.
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
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102486"
---
# <a name="azure-production-network"></a>Azure-produktionsnätverket
Användare av Azure-produktionsnätverket omfattar externa kunder som har åtkomst till sina egna Microsoft Azure-program som interna Microsoft Azure-supportpersonal som hanterar produktionsnätverket. Säkerhetsmetoder för åtkomst och skyddsmekanismer för att upprätta anslutningar till Azure-produktionsnätverket behandlas i den här artikeln.

## <a name="internet-routing-and-fault-tolerance"></a>Internet-Routning och feltolerans
Ett globalt redundant interna och externa Microsoft Azure Domain Name Service (WADNS) infrastrukturen tillsammans med flera primära och sekundära Domain Name Service (DNS) serverkluster tillhandahåller för feltolerans när ytterligare Microsoft Azure-nätverk säkerhetskontroller som NetScaler används för att förhindra angrepp Distributed Denial of Service (DDoS) och skydda integriteten hos Microsoft Azure DNS-tjänster.

WADNS-servrar finns i flera datacenter lokaler. WADNS-implementering har en hierarki av sekundär eller den primära DNS-servrar för att lösa domännamn i Azure kunden offentligt. Domännamnen motsvara vanligtvis en CloudApp.net adress som radbryts virtuella IP-adresser (VIP)-adressen för kundens tjänsten. Unik till Azure, VIP motsvarar interna dedikerade IP-Adressen (DIP)-adressen för klienten översättningen görs av belastningsutjämnare Microsoft ansvarar för att VIP.

Azure är värd för geografiskt distribuerade Azure-datacenter i USA och bygger på den senaste routning plattformar implementera robusta och skalbar arkitektur standarder. Här är några av de viktiga funktionerna:

- Multiprotokoll etikett växlar (MPLS) baserad trafik tekniker tillhandahåller effektiv länkanvändning och korrekt försämring av tjänsten om det finns ett avbrott
- Nätverk som implementeras med ”måste plus ett” (N + 1) redundans arkitekturer eller bättre.
- Externt, hanteras Datacenter av dedikerade nätverk med hög bandbredd kretsar som ansluter redundant egenskaper med över 1 200 Internetleverantörer globalt på flera peering punkter som tillhandahåller mer än 2 000 GB per sekund (Gbps) för Edge-kapacitet.

Eftersom Microsoft äger sin egen nätverket kretsar mellan datacenter, attributen hjälpa Azure erbjudandet uppnå 99.9 + % nätverkets tillgänglighet utan behov av traditionella från tredje part Internet-leverantörer.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Anslutning till företagets nätverk och associerade brandväggar
Azure-nätverk Internet-trafik flödet princip dirigerar trafik till Azure produktionsnätverket finns i det närmaste regionala datacentret i USA. Eftersom Azure produktion Datacenter underhåller konsekvent nätverksarkitektur och maskinvara, den nedan trafikflöde beskrivning gäller konsekvent för alla datacenter.

När Internet-trafik för Azure dirigeras till det närmaste datacentret, upprätta en anslutning till access-routrar. Dessa åtkomst routrar fungera att isolera trafik mellan Azure-noder och kunden instansierad virtuella datorer. Infrastruktur för nätverksenheter åtkomst och kanten platser är gräns punkterna där ingång och utgång filtren. Dessa routrar konfigureras via en skiktad ACL att filtrera oönskad trafik och koppla trafik hastighetsbegränsningar, om det behövs. Trafik som tillåts av ACL dirigeras till belastningsutjämnare. Distribution routrar är utformade för att tillåta endast Microsoft-godkända IP-adresser, ger skydd mot förfalskning och etablerade TCP-anslutningar med ACL: er.

Externa belastningsutjämning enheter bakom åtkomst-routrar för att utföra NAT (Network Address Translation) från Internet-dirigerbara IP-adresser till Azure interna IP-adresser. De också dirigera paket till giltiga produktion interna IP: er och portar och fungera som en skyddsmekanism att begränsa exponera interna produktion nätverks-adressutrymme.

Som standard tillämpar Microsoft Hypertext Transfer Protocol Secure (HTTPS) för all trafik som skickas till kundens webbplats webbläsare, inklusive logga in och all trafik därefter. Användningen av TLS version 1.2 gör en säker tunnel för trafik kan passera. Åtkomstkontrollistor för åtkomst och core routrar kontrollera källan för trafiken är konsekvent med den förväntade.

En viktig skillnad i den här arkitekturen jämfört med traditionell säkerhetsarkitekturen är att det finns inga dedikerad maskinvarubrandväggar, specialiserade intrång identifiering/förebyggande enheter eller andra säkerhetsenheter som normalt förväntat innan anslutningar görs till Azure-produktionsmiljö. Kunder kräver vanligtvis dessa maskinvarubrandväggar i Azure-nätverk; Det finns dock ingen anställda i Azure. Nästan uteslutande är dessa säkerhetsfunktioner inbyggda i Azure-miljön för att tillhandahålla robust flera lager säkerhetsmekanismer inklusive brandväggsfunktioner för programvaran. Dessutom är omfattning gräns och associerade svällande av kritiska säkerhetsenheter enklare att hantera och som visas i bilden ovan eftersom den hanteras av programmet som körs i Azure.

## <a name="core-security-and-firewall-features"></a>Grundläggande funktioner för säkerhet och brandvägg
Azure implementerar robusta säkerhet och brandväggen funktioner på olika nivåer att genomdriva säkerhetsfunktioner vanligtvis förväntas i en miljö med traditionella skydda core säkerhet auktorisering gräns.

### <a name="azure-security-features"></a>Azure säkerhetsfunktioner
Azure implementerar värdbaserad programvarubrandväggar i produktionsmiljön. Flera grundläggande säkerhet och brandväggen funktioner finns i grundläggande Azure-miljön. Dessa säkerhetsfunktioner avspeglar en strategi för skydd på djupet i Azure-miljön. Kundinformation i Microsoft Azure är skyddade av följande brandväggar:

**Hypervisor-brandväggen (paketfilter)**: den här brandväggen är implementerad i hypervisor-programmet och konfigurerad av FC-agenten. Den här brandväggen skyddar klienten körs på den virtuella datorn från obehörig åtkomst. Som standard när en virtuell dator skapas all trafik blockeras och FC-agenten sedan lägger till regler/undantag i filtret för att tillåta auktoriserade trafik.

Det finns två typer av regler som är programmerade här:

- Datorn Config eller infrastruktur regler: som standard all kommunikation är blockerad. Det finns undantag så att en virtuell dator att skicka och ta emot DHCP Dynamic Host Configuration Protocol ()-kommunikation, DNS-information, skicka trafik till ”offentliga” Internet utgående till andra virtuella datorer i FC-klustret och OS Aktiveringsservern. Eftersom de virtuella datorerna tillåts lista över utgående mål innehåller inte Microsoft Azure-router-undernät och andra Microsoft-egenskaper, reglerna fungera som en försvarslinje för dem.
- Konfigurationsfilen för rollen: Definierar inkommande ACL: erna baserat på klienternas modell. Till exempel om en klient har en frontwebb på port 80 på en viss virtuell dator och port 80 öppnas alla IP-adresser. Om den virtuella datorn har en arbetsroll som körs, öppnas arbetsrollen endast till den virtuella datorn i samma klientorganisation.

**Intern Värdbrandväggen**: Microsoft Azure Fabric och lagring körs på ett enhetligt operativsystem som har inga Hypervisor och därför Windows-brandväggen är konfigurerad med ovan två uppsättningar med regler.

**Värd för brandväggen**: värdbrandväggen skyddar värdpartitionen som körs hypervisor-programmet. Reglerna programmerad att tillåta endast FC och gå att kommunicera med värdpartitionen på en viss port. De övriga undantagen är att tillåta DHCP- och DNS-svar. Azure använder en datorkonfigurationen-fil som har mallen brandväggsregler för värdpartitionen. Det finns också ett brandväggsundantag för värden som gör att virtuella datorer att kommunicera med värd-komponenter, överföring server & metadataserver via specifika protocol-portar.

**Gästen brandväggen**: Windows-brandväggen typ av gäst-OS, som kan konfigureras av kund på kundens virtuella datorer och lagring.

Ytterligare säkerhetsfunktioner för inbyggda i Azure-funktioner:

- Infrastrukturkomponenter tilldelas IP-adresser från särskilda IP-adresser (korta). En angripare på Internet kan inte skicka trafik till dessa adresser eftersom de inte kan nå Microsoft. Internetgateway routrar filtrerar paket endast riktas till interna adresser så att de inte vill ange produktionsnätverket. Endast komponenter som accepterar trafik dirigeras till VIP-adresser är belastningsutjämnare.
- Brandväggar implementeras på alla interna noder har tre primära arkitektur säkerhetsaspekter för alla aktuella scenariot:

   - De placeras bakom belastningen belastningsutjämnare (LB) och acceptera paket från var som helst. Dessa är avsedda att exponeras externt och skulle motsvarar öppna portar i brandväggen traditionella perimeternätverk.
   - Endast acceptera paket från en begränsad uppsättning adresser. Detta är en del av defense djupgående strategi mot DOS-attacker. Dessa anslutningar autentiseras kryptografiskt.
   - Brandväggar kan endast nås från väljer interna noder, i vilket fall de accepterar paket från en uppräkningslistan för källans IP-adresser som är korta i Azure-nätverk. Till exempel ett angrepp på företagsnätverket kan dirigera begäranden till dessa adresser, men de skulle blockeras såvida inte källadress paketets var en i uppräkningslistan i Azure-nätverk.
   - Åtkomst-routern på perimeternätverket blockerar utgående paket som är adresserade till en adress som är i Azure-nätverk på grund av dess konfigurerade statiska vägar.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
