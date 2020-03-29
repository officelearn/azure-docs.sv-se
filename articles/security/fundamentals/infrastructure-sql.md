---
title: Säkerhetsfunktioner i Azure SQL Database
description: Den här artikeln innehåller en allmän beskrivning av hur Azure SQL Database skyddar kunddata i Azure.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942958"
---
# <a name="azure-sql-database-security-features"></a>Säkerhetsfunktioner i Azure SQL Database    
Azure SQL Database tillhandahåller en relationsdatabastjänst i Azure. För att skydda kunddata och tillhandahålla starka säkerhetsfunktioner som kunderna förväntar sig av en relationsdatabastjänst har SQL Database sina egna uppsättningar säkerhetsfunktioner. Dessa funktioner bygger på de kontroller som ärvs från Azure.

## <a name="security-capabilities"></a>Säkerhetsfunktioner

### <a name="usage-of-the-tds-protocol"></a>Användning av TDS-protokollet
Azure SQL Database stöder endast TDS-protokollet (Tabular Data Stream), som kräver att databasen endast är tillgänglig via standardporten för TCP/1433.

### <a name="azure-sql-database-firewall"></a>Brandvägg för Azure SQL Database
För att skydda kunddata innehåller Azure SQL Database en brandväggsfunktion som som standard förhindrar all åtkomst till SQL Database-servern, som visas nedan.

![Brandvägg för Azure SQL Database](./media/infrastructure-sql/sql-database-firewall.png)

Gateway-brandväggen kan begränsa adresser, vilket gör att kundernas detaljerade kontroll kan ange intervall för godtagbara IP-adresser. Brandväggen beviljar åtkomst baserat på den ursprungliga IP-adressen för varje begäran.

Kunder kan uppnå brandväggskonfiguration med hjälp av en hanteringsportal eller programmässigt med hjälp av AZURE SQL Database Management REST API. Azure SQL Database gateway-brandväggen förhindrar som standard alla kund-TDS-åtkomst till Azure SQL-databasinstanser. Kunder måste konfigurera åtkomst med hjälp av åtkomstkontrollistor (ACL: er) för att tillåta Azure SQL Database-anslutningar efter käll- och målinternetadresser, protokoll och portnummer.

### <a name="dosguard"></a>DosGuard (svenska)
DoS-attacker (Denial of Service) minskas av en SQL Database gateway-tjänst som kallas DoSGuard. DoSGuard spårar aktivt misslyckade inloggningar från IP-adresser. Om det finns flera misslyckade inloggningar från en viss IP-adress inom en tidsperiod blockeras IP-adressen från att komma åt resurser i tjänsten under en fördefinierad tidsperiod.

Dessutom utför Azure SQL Database-gatewayen:

- Förhandlingar om säker kanalkapacitet för att implementera TDS FIPS 140-2-validerade krypterade anslutningar när den ansluter till databasservrarna.
- Tillståndskänslig TDS-paketinspektion medan den accepterar anslutningar från klienter. Gatewayen validerar anslutningsinformationen och skickar TDS-paketen till rätt fysisk server baserat på databasnamnet som anges i anslutningssträngen.

Den övergripande principen för nätverkssäkerhet för Azure SQL Database-erbjudandet är att endast tillåta den anslutning och kommunikation som krävs för att tjänsten ska kunna fungera. Alla andra portar, protokoll och anslutningar blockeras som standard. Virtuella lokala nätverk (VLAN) och ACL:er används för att begränsa nätverkskommunikation via käll- och målnätverk, protokoll och portnummer.

Mekanismer som är godkända för att implementera nätverksbaserade åtkomstkontrollistor inkluderar ACL:er på routrar och belastningsutjämnare. Dessa mekanismer hanteras av Azure-nätverk, gäst-VM-brandvägg och Azure SQL Database gateway brandväggsregler, som är konfigurerade av kunden.

## <a name="data-segregation-and-customer-isolation"></a>Datasegregering och kundisolering
Azure-produktionsnätverket är strukturerat så att allmänt tillgängliga systemkomponenter är åtskilda från interna resurser. Det finns fysiska och logiska gränser mellan webbservrar som ger åtkomst till den offentliga Azure-portalen och den underliggande virtuella Azure-infrastrukturen, där kundprograminstanser och kunddata finns.

All offentligt tillgänglig information hanteras i Azure-produktionsnätverket. Produktionsnätverket är föremål för tvåfaktorsautentiserings- och gränsskyddsmekanismer, använder brandväggs- och säkerhetsfunktionsuppsättningen som beskrivs i föregående avsnitt och använder dataisoleringsfunktioner som anges i nästa avsnitt.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Otillåtna system och isolering av FC
Eftersom fabric controller (FC) är den centrala orchestrator av Azure-infrastrukturen, är betydande kontroller på plats för att minska hot mot den, särskilt från potentiellt komprometterade FAs inom kundprogram. FC känner inte igen någon maskinvara vars enhetsinformation (till exempel MAC-adress) inte är förinläst i FC. DHCP-servrarna på FC har konfigurerat listor över MAC-adresser till de noder som de är villiga att starta. Även om obehöriga system är anslutna, är de inte införlivas i tyg inventering, och därför inte ansluten eller auktoriserad att kommunicera med något system i tyget inventering. Detta minskar risken för att obehöriga system kommunicerar med FC och får åtkomst till VLAN och Azure.

### <a name="vlan-isolation"></a>VLAN-isolering
Azure-produktionsnätverket är logiskt segregerat i tre primära VLAN:

- Huvud-VLAN: Sammanlänkningar som inte betrodda kundnoder.
- FC VLAN: Innehåller betrodda FCs och stödsystem.
- Enheten VLAN: Innehåller betrodda nätverk och andra infrastrukturenheter.

### <a name="packet-filtering"></a>Paketfiltrering
IPFilter och programvaran brandväggar som implementeras på roten OS och gäst OS av noderna genomdriva anslutningsbegränsningar och förhindra obehörig trafik mellan virtuella datorer.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, rot-OS och gäst-virtuella datorer
Isoleringen av rotoperativsystemet från gäst-virtuella datorer och gäst-virtuella datorer från varandra hanteras av hypervisorn och rotoperativsystemet.

### <a name="types-of-rules-on-firewalls"></a>Typer av regler för brandväggar
En regel definieras som:

{Src IP, Src-port, mål-IP, målport, målprotokoll, in/ut, tillståndskänslig/tillståndslös, tillståndskänslig flödestidout}.

Synkron inaktiva teckenpaket (SYN) tillåts endast in eller ut om någon av reglerna tillåter det. För TCP använder Azure tillståndslösa regler där principen är att den endast tillåter alla icke-SYN-paket till eller från den virtuella datorn. Säkerhetspremissen är att alla värdstapel är motståndskraftigt mot att ignorera en icke-SYN om den inte har sett ett SYN-paket tidigare. TCP-protokollet i sig är tillståndskänsligt och i kombination med den tillståndslösa SYN-baserade regeln uppnår ett övergripande beteende för en tillståndskänslig implementering.

För UDP (User Datagram Protocol) använder Azure en tillståndskänslig regel. Varje gång ett UDP-paket matchar en regel skapas ett omvänt flöde i den andra riktningen. Det här flödet har en inbyggd timeout.

Kunderna ansvarar för att konfigurera sina egna brandväggar ovanpå vad Azure tillhandahåller. Här kan kunderna definiera reglerna för inkommande och utgående trafik.

### <a name="production-configuration-management"></a>Hantering av produktionskonfigurationer
Standardsäkra konfigurationer underhålls av respektive driftteam i Azure och Azure SQL Database. Alla konfigurationsändringar i produktionssystem dokumenteras och spåras via ett centralt spårningssystem. Ändringar av programvara och maskinvara spåras via det centrala spårningssystemet. Nätverksändringar som relaterar till ACL spåras med hjälp av en ACL-hanteringstjänst.

Alla konfigurationsändringar i Azure utvecklas och testas i mellanlagringsmiljön och de distribueras därefter i produktionsmiljö. Programvarubyggen granskas som en del av testningen. Säkerhets- och sekretesskontroller granskas som en del av checklista för inresa. Ändringar distribueras på schemalagda intervall av respektive distributionsteam. Versioner granskas och signeras av respektive distributionsgruppspersonal innan de distribueras i produktion.

Förändringar övervakas för framgång. I ett felscenario återställs ändringen till sitt tidigare tillstånd eller så distribueras en snabbkorrigering för att åtgärda felet med godkännande av den angivna personalen. Source Depot, Git, TFS, Master Data Services (MDS), löpare, Azure-säkerhetsövervakning, FC och WinFabric-plattformen används för att centralt hantera, tillämpa och verifiera konfigurationsinställningarna i den virtuella Azure-miljön.

På samma sätt har maskinvaru- och nätverksändringar upprättat valideringssteg för att utvärdera att de följer byggkraven. Utgåvorna granskas och godkänns genom en samordnad ändringsråd (CAB) av respektive grupper i stacken.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
