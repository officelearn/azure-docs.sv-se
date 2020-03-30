---
title: Azure-produktionsnätverk
description: Den här artikeln innehåller en allmän beskrivning av Azure-produktionsnätverket.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726697"
---
# <a name="the-azure-production-network"></a>Azure-produktionsnätverket
Användarna av Azure-produktionsnätverket inkluderar både externa kunder som har åtkomst till sina egna Azure-program och intern Azure-supportpersonal som hanterar produktionsnätverket. I den här artikeln beskrivs metoder för säkerhetsåtkomst och skyddsmekanismer för upprättande av anslutningar till Azure-produktionsnätverket.

## <a name="internet-routing-and-fault-tolerance"></a>Internetroutning och feltolerans
En globalt redundant intern och extern DNS-infrastruktur (Azure Domain Name Service), kombinerat med flera primära och sekundära DNS-serverkluster, ger feltolerans. Samtidigt används ytterligare Azure-nätverkssäkerhetskontroller, till exempel NetScaler, för att förhindra ddos-attacker (Distributed Denial of Service) och skydda integriteten för Azure DNS-tjänster.

Azure DNS-servrarna finns vid flera datacenterresurser. Azure DNS-implementeringen innehåller en hierarki med sekundära och primära DNS-servrar för att offentligt matcha Azure-kunddomännamn. Domännamnen matchas vanligtvis till en CloudApp.net-adress, som radbryts den virtuella IP-adressen (VIP) för kundens tjänst. Unik för Azure, VIP som motsvarar interna dedikerade IP (DIP) adress för klientöversättningen görs av Microsoft belastningsutjämnare som ansvarar för att VIP.

Azure finns i geografiskt distribuerade Azure-datacenter i USA och bygger på toppmoderna routningsplattformar som implementerar robusta, skalbara arkitektoniska standarder. Bland de anmärkningsvärda funktionerna är:

- Multiprotocol Label Switching (MPLS)-baserad trafikteknik, som ger effektiv länkutnyttjande och graciös försämring av tjänsten om det finns ett avbrott.
- Nätverk implementeras med "behov plus ett" (N+1) redundansarkitekturer eller bättre.
- Externt betjänas datacenter av dedikerade nätverkskretsar med hög bandbredd som redundant kopplar samman egenskaper med över 1 200 internetleverantörer globalt vid flera peering-punkter. Den här anslutningen ger över 2 000 GB per sekund (GBps) kantkapacitet.

Eftersom Microsoft äger sina egna nätverkskretsar mellan datacenter hjälper dessa attribut Azure-erbjudandet att uppnå 99,9+ procent nätverkstillgänglighet utan att behöva traditionella internetleverantörer från tredje part.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Anslutning till produktionsnätverk och tillhörande brandväggar
Azure-internettrafikflödesprincipen dirigerar trafik till Azure-produktionsnätverket som finns i närmaste regionala datacenter i USA. Eftersom Azure-produktionsdata datacenter upprätthåller konsekvent nätverksarkitektur och maskinvara gäller trafikflödesbeskrivningen som följer konsekvent för alla datacenter.

När internettrafik för Azure dirigeras till närmaste datacenter upprättas en anslutning till åtkomstroutrarna. Dessa åtkomstroutrar används för att isolera trafik mellan Azure-noder och kund-instansierade virtuella datorer. Nätverksinfrastrukturenheter på åtkomst- och kantplatserna är de gränspunkter där ingående och utgående filter tillämpas. Dessa routrar konfigureras via en nivåindelade åtkomstkontrolllista (ACL) för att filtrera oönskad nätverkstrafik och tillämpa trafikhastighetsgränser om det behövs. Trafik som tillåts av ACL dirigeras till belastningsutjämnare. Distributionsroutrar är utformade för att endast tillåta Microsoft-godkända IP-adresser, tillhandahålla förfalskning och upprätta TCP-anslutningar som använder ÅTKOMSTKONTROLL.Distributionsrouter are designed to allow only Microsoft-approved IP addresses, provide anti-spoofing, and establish TCP connections that use ACLs.

Externa belastningsutjämningsenheter finns bakom åtkomstroutrarna för att utföra NAT (Network Address Translation) från internetdirigerbara IP-adresser till virtuella Azure-IP-adresser. Enheterna dirigerar också paket till giltiga interna installationer och portar för produktion, och de fungerar som en skyddsmekanism för att begränsa utsätta det interna produktionsnätverkets adressutrymme.

Som standard tillämpar Microsoft HTTPS (Hypertext Transfer Protocol Secure) för all trafik som överförs till kundernas webbläsare, inklusive inloggning och all trafik därefter. Användningen av TLS v1.2 möjliggör en säker tunnel för trafik att flöda igenom. ACL:er på åtkomst- och kärnroutrar säkerställer att trafikkällan överensstämmer med vad som förväntas.

En viktig skillnad i den här arkitekturen, när den jämförs med traditionell säkerhetsarkitektur, är att det inte finns några dedikerade maskinvarubrandvägger, specialiserade intrångsidentifierings- eller förhindrande enheter eller andra säkerhetsapparater som normalt innan anslutningar görs till Azure-produktionsmiljön. Kunderna förväntar sig vanligtvis dessa maskinvarubrandväggsenheter i Azure-nätverket. Ingen är dock anställd i Azure. Nästan uteslutande är dessa säkerhetsfunktioner inbyggda i programvaran som kör Azure-miljön för att tillhandahålla robusta säkerhetsmekanismer i flera lager, inklusive brandväggsfunktioner. Dessutom är omfattningen av gränsen och tillhörande utbredning av kritiska säkerhetsenheter lättare att hantera och inventera, som visas i föregående bild, eftersom det hanteras av programvaran som kör Azure.

## <a name="core-security-and-firewall-features"></a>Grundläggande säkerhets- och brandväggsfunktioner
Azure implementerar robusta programsäkerhets- och brandväggsfunktioner på olika nivåer för att framtvinga säkerhetsfunktioner som vanligtvis förväntas i en traditionell miljö för att skydda den grundläggande säkerhetsauktoriseringsgränsen.

### <a name="azure-security-features"></a>Azure-säkerhetsfunktioner
Azure implementerar värdbaserade programvarubrandvägger i produktionsnätverket. Flera grundläggande säkerhets- och brandväggsfunktioner finns i azure-kärnmiljön. Dessa säkerhetsfunktioner återspeglar en djupgående strategi i Azure-miljön. Kunddata i Azure skyddas av följande brandväggar:

**Hypervisor-brandvägg (paketfilter)**: Den här brandväggen implementeras i hypervisorn och konfigureras av agenten för fabric controller (FC). Den här brandväggen skyddar klienten som körs inuti den virtuella datorn från obehörig åtkomst. När en virtuell dator skapas blockeras all trafik som standard och sedan lägger FC-agenten till regler och undantag i filtret för att tillåta auktoriserad trafik.

Två kategorier av regler är programmerade här:

- **Maskinkonfiguration eller infrastrukturregler**: Som standard blockeras all kommunikation. Det finns undantag som gör det möjligt för en virtuell dator att skicka och ta emot DHCP-kommunikation och DNS-information (Dynamic Host Configuration Protocol) och skicka trafik till den "offentliga" internet som skickas ut till andra virtuella datorer i FC-klustret och operativsystemets aktiveringsserver. Eftersom de virtuella datorernas tillåtna lista över utgående destinationer inte innehåller Azure-routerns undernät och andra Microsoft-egenskaper fungerar reglerna som ett försvarslager för dem.
- **Rollkonfigurationsfilregler**: Definierar de inkommande ACL:erna baserat på klienternas tjänstmodell. Om en klient har en webbklient på port 80 på en viss virtuell dator öppnas port 80 till alla IP-adresser. Om den virtuella datorn har en arbetarroll som körs öppnas arbetarrollen endast för den virtuella datorn i samma klient.

**Inbyggd värdbrandvägg:** Azure Service Fabric och Azure Storage körs på ett inbyggt operativsystem, som inte har någon hypervisor och därför är Windows-brandväggen konfigurerad med föregående två uppsättningar regler.

**Värdbrandvägg:** Värdbrandväggen skyddar värdpartitionen, som kör hypervisorn. Reglerna är programmerade så att endast FC och hopprutor att prata med värdpartitionen på en viss port. De andra undantagen är att tillåta DHCP-svar och DNS-svar. Azure använder en konfigurationsfil för datorer som innehåller en mall med brandväggsregler för värdpartitionen. Det finns också ett undantag från värdbrandväggen som gör att virtuella datorer kan kommunicera till värdkomponenter, trådserver och metadataserver via specifika protokoll/portar.

**Gästbrandvägg:** Windows-brandväggen i gästoperativsystemet, som kan konfigureras av kunder på virtuella kunddatorer och lagring.

Ytterligare säkerhetsfunktioner som är inbyggda i Azure-funktionerna är:

- Infrastrukturkomponenter som tilldelas IP-adresser som kommer från DIPs. En angripare på internet kan inte adressera trafik till dessa adresser eftersom den inte skulle nå Microsoft. Internet gateway-routrar filtrerar paket som endast är adresserade till interna adresser, så att de inte kommer in i produktionsnätverket. De enda komponenter som accepterar trafik som dirigeras till VIP är belastningsutjämnare.
- Brandväggar som implementeras på alla interna noder har tre primära säkerhetsarkitekturöverväganden för ett visst scenario:

   - Brandväggar placeras bakom belastningsutjämnaren och accepterar paket var som helst. Dessa paket är avsedda att exponeras externt och skulle motsvara de öppna portarna i en traditionell perimeterbrandvägg.
   - Brandväggar accepterar paket endast från en begränsad uppsättning adresser. Detta övervägande är en del av den defensiva djupgående strategin mot DDoS-attacker. Sådana anslutningar autentiseras kryptografiskt.
   - Brandväggar kan endast nås från utvalda interna noder. De accepterar paket endast från en uppräknad lista över käll-IP-adresser, som alla är DIPs i Azure-nätverket. En attack mot företagsnätverket kan till exempel rikta begäranden till dessa adresser, men attackerna skulle blockeras om inte paketets källadress var en i den uppräknade listan i Azure-nätverket.
     - Åtkomstroutern vid perimetern blockerar utgående paket som är adresserade till en adress som finns i Azure-nätverket på grund av dess konfigurerade statiska vägar.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
