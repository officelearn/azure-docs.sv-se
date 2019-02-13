---
title: Azure SQL Database-säkerhetsfunktioner
description: Den här artikeln innehåller en allmän beskrivning av hur Azure SQL Database skyddar kundinformation i Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103956"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database-säkerhetsfunktioner    
Azure SQL Database tillhandahåller en relationsdatabastjänst i Azure. För att skydda kunddata och ger starka säkerhetsfunktioner som kunderna förväntar sig av en relationsdatabastjänst, har SQL Database en egen uppsättning funktioner för säkerhet. Dessa funktioner bygger på de kontroller som ärvs från Azure.

## <a name="security-capabilities"></a>Säkerhetsfunktioner

### <a name="usage-of-the-tds-protocol"></a>Användning av TDS-protokoll
Azure SQL Database stöder bara tabelldata stream (TDS)-protokollet, vilket kräver att databasen är tillgänglig över bara i standard port för TCP/1433.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database-brandvägg
För att skydda kunddata, innehåller Azure SQL Database en brandväggsfunktioner, vilket som standard förhindrar all åtkomst till SQL Database-server som visas nedan.

![Azure SQL Database-brandvägg][1]

Gateway-brandväggen kan begränsa adresser, vilket gör att kunderna detaljerad kontroll att ange intervall med godkända IP-adresser. Brandväggen ger åtkomst baserat på den ursprungliga IP-adressen för varje begäran.

Kunderna kan nå brandväggskonfiguration med hjälp av en hanteringsportalen eller via programmering med hjälp av Azure SQL Database Management REST-API. Azure SQL Database gateway-brandväggen som standard förhindrar alla kund TDS-åtkomst till Azure SQL database-instanser. Kunder måste konfigurera åtkomst med hjälp av åtkomstkontroll listor (ACL) för att tillåta anslutningar i Azure SQL Database som källa och mål internet-adresser, protokoll och portnummer.

### <a name="dosguard"></a>DoSGuard
Attacker denial of service (DoS) minskas med en gateway-tjänsten för SQL-databas som heter DoSGuard. DoSGuard spårar aktivt misslyckade inloggningar från IP-adresser. Om det finns flera misslyckade inloggningar från en specifik IP-adress inom en viss tidsperiod, är IP-adressen blockerad från åtkomst till resurser i tjänsten för en fördefinierad tidsperiod.

Dessutom utför Azure SQL Database-gatewayen:

- Säker kanal funktionen förhandlingar att implementera TDS FIPS 140-2-verifierade krypterade anslutningar när den ansluter till databasservrarna.
- Tillståndskänsliga TDS-paketinspektion när den tar emot anslutningar från klienter. Gatewayen verifierar anslutningsinformationen och skickar på TDS-paket till rätt fysisk server baserat på namnet på databasen som anges i anslutningssträngen.

Övergripande princip för nätverkssäkerhet för Azure SQL Database-erbjudande är att tillåta endast anslutning och kommunikation som krävs för att tjänsten ska fungera. Alla andra portar, protokoll och anslutningar blockeras som standard. Virtuellt lokalt nätverk (VLAN) och ACL: er används för att begränsa nätverkskommunikationen av käll- och nätverk, protokoll och portnummer.

Metoder som är godkända för att implementera nätverksbaserade ACL: er omfattar ACL: er på routrar och belastningsutjämnare. Dessa mekanismer som hanteras av Azure-nätverk, Virtuella gästdatorns brandvägg och Azure SQL Database gateway brandväggsregler, som konfigureras av kunden.

## <a name="data-segregation-and-customer-isolation"></a>Dataisolering särskiljning och kunden
Azure-produktionsnätverket struktureras så att offentligt tillgänglig systemkomponenter är åtskilda från interna resurser. Fysisk och logisk gränser finns mellan webbservrar som ger åtkomst till offentliga Azure-portalen och den underliggande Azure virtuella infrastrukturen där kunden programinstanser och kunddata finns.

All information som är offentligt tillgänglig hanteras i Azure-produktionsnätverket. Produktionsnätverket är föremål för tvåfaktorsautentisering och gränsen skyddet, använder funktionsuppsättningen brandväggs- och säkerhetskonfiguration som beskrivs i föregående avsnitt och använder isolering datafunktioner enligt vad som anges i nästa avsnitt.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Obehörig system och isolering av FC
Eftersom infrastrukturkontrollanten (FC) är central dirigering Azure-infrastrukturen, är betydande kontroller på plats för att identifiera hot, särskilt från potentiellt komprometterade FAs i kundernas tillämpningar. FC känner inte någon maskinvara vars enhetsinformation (till exempel MAC-adress) inte laddas före inom FC. DHCP-servrar i FC har konfigurerat en lista över MAC-adresserna för de noder som de är villig att starta. Även om obehöriga system är anslutna de inte ingår i fabric lager, och därför inte är ansluten eller behörighet att kommunicera med alla system inom fabric inventeringen. Detta minskar risken för obehörig system kommunicerar med FC och få åtkomst till VLAN och Azure.

### <a name="vlan-isolation"></a>VLAN-isolering
Azure-produktionsnätverket är logiskt indelad i tre primära VLAN:

- Det huvudsakliga VLANET: Sammanbinder obetrodda kunden noder.
- FC-VLAN: Innehåller betrodda FCs och ge support för system.
- Enheten VLAN: Innehåller betrott nätverk och andra infrastruktur-enheter.

### <a name="packet-filtering"></a>Filtrering av nätverkspaket
IPFilter och programvarubrandväggar som implementeras på rot OS- och Gäst-OS nodernas tillämpa begränsningar för anslutning och förhindrar att otillåten trafik mellan virtuella datorer.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor-programmet och rot OS-gästdatorer
Isolering av roten OS från virtuella gästdatorerna och virtuella gästdatorerna från varandra hanteras av hypervisor-programmet och roten OS.

### <a name="types-of-rules-on-firewalls"></a>Typer av regler för brandväggar
En regel definieras som:

{Security Response Center (Src) IP-adress, källport, mål IP, målport, mål-protokollet, In/ut tillståndskänsliga/tillståndslösa och tillståndskänsliga Flow Timeout}.

Synkron inaktiv tecken (SYN) paket tillåts in eller ut endast om någon av reglerna tillåter den. För TCP använder Azure tillståndslösa regler där principen är att den tillåter endast alla icke-SYN-paket till eller från den virtuella datorn. Security antagandet är att valfri värd-stack kan återhämtas ignoreras icke-SYN om SYN-paket inte påträffats tidigare. Själva TCP-protokollet är tillståndskänslig och ger en övergripande beteendet för en tillståndskänslig implementering i kombination med den tillståndslösa SYN-baserade regler.

För User Datagram Protocol (UDP) i Azure använder en tillståndskänslig regel. Varje gång ett UDP-paket matchar en regel, skapas en omvänd flödet i den andra riktningen. Det här flödet har en inbyggd tidsgräns.

Kunderna ansvarar för att konfigurera sina egna brandväggar ovanpå Azure tillhandahåller. Här följer kunder kan definiera regler för inkommande och utgående trafik.

### <a name="production-configuration-management"></a>Konfigurationshantering för produktion
Säker standardkonfigurationer underhålls av respektive driftteam i Azure och Azure SQL Database. Alla konfigurationsändringar till produktionssystem dokumenteras och spåras via en central spårningssystemet. Program- och ändringarna spåras via det centrala spårningssystemet. Nätverksändringar som relaterar till ACL spåras med hjälp av en ACL management-tjänsten.

Alla konfigurationsändringar till Azure utvecklas och testas i mellanlagringsmiljön och de därefter har distribuerats i produktionsmiljön. Programversioner granskas som en del av testning. Säkerhet och sekretess kontroller granskas som en del av posten checklista kriterier. Ändringar har distribuerats schemalagda intervall av respektive distributionsgruppen. Versioner granskat och godkänt av respektive distribution team personal innan de distribueras till produktionen.

Ändringar övervakas för att lyckas. Ändringen återställs till sitt tidigare tillstånd eller en snabbkorrigering distribueras för att åtgärda felet med godkännande av de avsedda personal på ett felscenario. Source Depot, Git, TFS, Master Data Services (MDS), deltagare, Azure säkerhetsövervakning, FC och WinFabric-plattformen används för att centralt hantera, tillämpa och verifiera konfigurationsinställningarna i Azure-virtuella miljön.

På samma sätt maskinvaru- och ändringar som har upprättat valideringssteg för att utvärdera sin efterlevnad av build-kraven. Versioner granskas och behörighet via en samordnad rådgivningsnämnden för ändringar (CAB) i respektive grupper i stacken.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
