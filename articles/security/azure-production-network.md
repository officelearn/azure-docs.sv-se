---
title: Azure-produktionsnätverket
description: Den här artikeln innehåller en allmän beskrivning av Azure-produktionsnätverket.
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
ms.openlocfilehash: 710792c890c3e48fc54507f93eeaee529ca839f8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114036"
---
# <a name="the-azure-production-network"></a>Azure-produktionsnätverket
Användare av Azure-produktionsnätverket omfattar både externa kunder som har åtkomst till sina egna Azure-program och intern Azure supportpersonal som hanterar produktionsnätverket. Den här artikeln beskrivs säkerhetsmetoder för åtkomst och skyddsmekanismer för att upprätta anslutningar till Azure-produktionsnätverket.

## <a name="internet-routing-and-fault-tolerance"></a>Internet-Routning och feltolerans
En globalt redundant interna och externa Azure tjänsten DNS (Domain Name) infrastruktur, i kombination med flera primära och sekundära DNS-server-kluster, ger feltolerans. På samma gång används säkerhetsåtgärder för fler Azure-nätverk, till exempel NetScaler, för att förhindra distribuerade DOS-attacker (DDoS) och för att skydda integriteten för Azure DNS-tjänster.

Azure DNS-servrar finns i flera datacenter anläggningar. Azure DNS-implementering införlivar en hierarki av sekundära och primär DNS-servrar för att lösa offentligt domännamn i Azure-kund. Vanligtvis lösa domännamn till en CloudApp.net-adress, som omsluter den virtuella IP (VIP)-adressen för kundens-tjänsten. Unik till Azure, den VIP som motsvarar interna dedikerad IP (DIP)-adressen för klienten översättningen gör du genom de Microsoft-belastningsutjämnarna som ansvarar för den VIP.

Azure är värd för geografiskt distribuerade Azure-datacenter i USA och den är byggd på den senaste routning plattformar som implementerar robusta, skalbara arkitektoniska standarder. Bland de viktiga funktionerna är:

- Multiprotokoll etikett växla MPLS-baserad trafik engineering, vilket ger utnyttjandet effektiv och korrekt försämring av tjänsten om det uppstår ett avbrott.
- Nätverk som implementeras med ”behöver plus ett” (N + 1) redundans arkitekturer eller bättre.
- Externt, betjänas Datacenter av dedikerade nätverk med hög bandbredd krets för att ansluta redundant egenskaper med över 1 200 Internetleverantörer globalt på flera platser för peering. Den här anslutningen innehåller som överstiger 2 000 GB per sekund (GBps) av edge-kapacitet.

Eftersom Microsoft äger sin egen kretsar för nätverk mellan datacenter, hjälper Azure-erbjudande uppnå 99,9 + procent Nätverkstillgänglighet utan behov av traditionella från tredje part för Internetleverantörer dessa attribut.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Anslutning till företagets nätverk och associerade brandväggar
Azure-nätverk internet traffic flow principen dirigerar trafik till Azure-produktionsnätverket som finns i det närmaste regionala datacentret i USA. Eftersom Azure produktion Datacenter behålla konsekvent nätverksarkitektur och maskinvara, gäller flödesbeskrivningen för trafik som följer konsekvent för alla datacenter.

När internet-trafik för Azure dirigeras till det närmaste datacentret, upprättas en anslutning till åtkomst-routrar. De här åtkomst-routrar fungera att isolera trafik mellan Azure-noder och kunden instansierats virtuella datorer. Infrastruktur för nätverksenheter på åtkomst och edge platser är gräns-punkter där ingående och utgående filter tillämpas. Dessa routrar konfigureras via en nivåindelad åtkomstkontroll åtkomstkontrollistan (ACL) och filtrera oönskad nätverkstrafik som du kan tillämpa trafik hastighetsbegränsningar, om det behövs. Trafik som tillåts av ACL dirigeras till för belastningsutjämnarna. Distribution routrar är utformade för att tillåta endast Microsoft-godkänd IP-adresser, ger skydd mot förfalskning och upprätta en TCP-anslutningar som använder ACL: er.

Extern belastningsutjämning enheter bakom åtkomst-routrar för att utföra network adress translation (NAT) från internet-dirigerbara IP-adresser till Azure interna IP-adresser. Enheterna också dirigera paket till giltiga produktion interna IP-adresser och portar, och de fungerar som en mekanism för att begränsa exponera interna produktion nätverkets adressutrymme.

Microsoft använder Hypertext Transfer Protocol Secure (HTTPS) för all trafik som skickas till kundens webbläsare, däribland inloggning och all trafik därefter som standard. Användningen av TLS version 1.2 gör en säker tunnel för trafik kan passera. ACL: er på åtkomst och core routrar Kontrollera att källan för trafiken är konsekvent med den förväntade.

En viktig skillnad i den här arkitekturen är när den är jämfört med traditionell Säkerhetsarkitektur, att det finns inga dedikerad maskinvarubrandväggar, specialiserad intrångsidentifiering eller förhindra enheter eller andra säkerhetsenheter som annars förväntat innan anslutningar görs till Azure produktionsmiljön. Kunderna förvänta sig dessa maskinvarubrandväggar i Azure-nätverk; dock är ingen anställda inom Azure. Nästan uteslutande är dessa säkerhetsfunktioner inbyggda i programvaran som körs i Azure-miljön för att tillhandahålla robust, flera lager säkerhetsmekanismer, inklusive brandväggsfunktioner. Dessutom är omfånget för gräns och associerade tillväxten av lagringsutrymme för kritiska säkerhetsenheter lättare att hantera och inventering, som visas i den föregående bilden, eftersom den hanteras av den programvara som körs på Azure.

## <a name="core-security-and-firewall-features"></a>Grundläggande funktioner för säkerhet och brandvägg
Azure implementerar robusta säkerhets- och funktioner för brandvägg på olika nivåer för att framtvinga säkerhetsfunktioner som förväntas vanligtvis i en miljö med traditionella som skyddar core Säkerhetsauktorisering gränsen.

### <a name="azure-security-features"></a>Azure säkerhetsfunktioner
Azure implementerar värdbaserade programvarubrandväggar i produktionsmiljön. Flera grundläggande säkerhet och funktioner för brandvägg finns i grundläggande Azure-miljön. Dessa säkerhetsfunktioner visas en strategi för skydd på djupet i Azure-miljön. Kundinformation i Azure är skyddade av följande brandväggar:

**Hypervisor-brandvägg (ett paketfilter)**: den här brandväggen implementeras i hypervisorn och konfigureras av infrastrukturkontrollantens (FC) agent. Den här brandväggen skyddar den klient som körs på den virtuella datorn från obehörig åtkomst. Som standard när en virtuell dator skapas blockeras all trafik och sedan FC-agenten lägger till regler och undantag i filtret och tillåta auktoriserad trafik.

Två typer av regler är programmerade här:

- **Datorn config eller infrastruktur regler**: som standard all kommunikation blockerad. Det finns undantag som gör att en virtuell dator för att skicka och ta emot DHCP Dynamic Host Configuration Protocol ()-kommunikation och DNS-information och skicka trafik till internet ”offentliga” utgående trafik till andra virtuella datorer i FC-klustret och Operativsystemets aktiveringsserver. Eftersom de virtuella datorerna får listan över utgående mål innehåller inte Azure-routerundernät och andra Microsoft-företag, reglerna fungerar som ett lager skydd för dessa.
- **Rollen fil konfigurationsregler**: definiera inkommande åtkomstkontrollistor baserat på klienternas tjänstmodell. Om en klient har en webbklientdel på port 80 på en viss virtuell dator, öppnas till exempel port 80 till alla IP-adresser. Om den virtuella datorn har en arbetsroll som körs, öppnas arbetsrollen endast till den virtuella datorn inom samma klientorganisation.

**Intern värdbrandvägg**: Azure Service Fabric och Azure Storage körs på ett internt operativsystem som inte har någon hypervisor, och därför Windows-brandväggen är konfigurerad med föregående två regeluppsättningar.

**Värdbrandvägg**: av värdens brandvägg skyddar värdpartitionen som kör hypervisor-programmet. Reglerna är programmerade för att tillåta endast FC och hopprutorna kommunicerar till värdpartitionen på en viss port. De övriga undantagen är att tillåta DHCP- och DNS-svar. Azure använder en konfigurationsfil för datorn som innehåller en mall med brandväggsregler för värdpartitionen. Ett brandväggsundantag för värden finns som kan virtuella datorer att kommunicera med värd-komponenter, wire-server och metadata-servern via specifika protocol/portar.

**Gästbrandvägg**: The Windows-brandväggsdelen av gästoperativsystemet, men kan ändras av kunder på kundens virtuella datorer och lagring.

Ytterligare funktioner som är inbyggda i Azure-funktioner:

- Infrastrukturkomponenter som är tilldelade IP-adresser från dalar. En angripare på internet kan inte-trafik till de adresserna eftersom det inte skulle att nå Microsoft. Internet-gateway routrar filtrera paket som är adresserade enbart till interna adresser, så att de inte anger produktionsnätverket. De enda komponenter som ta emot trafik som går till VIP-adresser är belastningsutjämnare.
- Brandväggar som implementeras på alla interna noder har tre primära arkitektur säkerhetsöverväganden för alla aktuella scenariot:

   - Brandväggar placeras bakom belastningsutjämnaren och acceptera paket från var som helst. Dessa paket ska exponeras externt och skulle motsvarar öppna portar i en traditionell gränsbrandväggen.
   - Brandväggar acceptera paket endast från en begränsad uppsättning adresser. Den här beräkningen är en del av skydden djupgående strategin mot DDoS-attacker. Dessa anslutningar autentiseras kryptografiskt.
   - Brandväggar kan nås från väljer interna noder. De accepterar paket endast från en uppräknade lista över källans IP-adresser som alla är dalar i Azure-nätverket. Till exempel ett angrepp på företagsnätverket kan dirigera begäranden till dessa adresser, men attackerna skulle blockeras såvida inte-källadressen för paketet har en i uppräkningslistan i Azure-nätverket.
     - Åtkomst-router i perimetern blockerar utgående paket som är adresserade till en adress som är i Azure-nätverket på grund av dess konfigurerade statiska vägar.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
