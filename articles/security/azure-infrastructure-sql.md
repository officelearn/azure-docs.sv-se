---
title: Azure SQL Database-säkerhetsfunktioner
description: Den här artikeln innehåller en allmän beskrivning av Azure SQL Database skyddar kundens data i Azure.
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
ms.openlocfilehash: cca8febb004029b13b0df09a047da701c4528e8e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102736"
---
# <a name="microsoft-azure-sql-database-security-features"></a>Microsoft Azure SQL Database-säkerhetsfunktioner    
Microsoft Azure SQL Database är en relationsdatabastjänst i Azure. För att skydda kundens data och starka säkerhetsfunktioner som kunder förväntar dig från en relationsdatabastjänst, har SQL Database sin egen uppsättning säkerhetsfunktioner. Dessa funktioner bygger på de kontroller som ärvs från Azure.

## <a name="security-capabilities"></a>Säkerhetsfunktioner

### <a name="usage-of-tabular-data-stream-tds-protocol"></a>Användning av Tabular Data Stream TDS-protokoll
Microsoft Azure SQL Database stöder endast TDS-protokollet, vilket kräver att databasen är tillgänglig endast via de standard port för TCP/1433.

### <a name="microsoft-azure-sql-database-firewall"></a>Microsoft Azure SQL Database-brandvägg
För att skydda kundens data, innehåller Microsoft Azure SQL Database en brandväggsfunktioner, vilket som standard förhindrar all åtkomst till SQL Database-server som visas nedan.

![Azure SQL Database-brandvägg][1]

Gateway-brandvägg ger möjlighet att begränsa adresser tillåter granulär kontroll för kunder att ange godkända IP-adressintervall. Brandväggen beviljar åtkomst baserat på den ursprungliga IP-adressen för varje begäran.

Brandväggskonfiguration kan åstadkommas med hjälp av en Management Portal eller programmässigt med hjälp av Microsoft Azure SQL Database Management REST API. Microsoft Azure SQL Database Gateway-brandväggen som standard förhindrar alla kunden TDS-åtkomst till Microsoft Azure SQL-databaser. Åtkomst måste konfigureras med ACL: er för att tillåta anslutningar i Microsoft Azure SQL Database av käll- och måladresser för Internet, protokoll och portnummer.

### <a name="dosguard"></a>DoSGuard
Denial of service (DoS) attacker minskas med en SQL-databas Gateway-tjänsten som kallas DoSGuard. DoSGuard spårar aktivt misslyckade inloggningar från IP-adresser. Om det finns flera misslyckade inloggningar från en specifik IP-adress inom en viss tidsperiod, har IP-adressen blockerats från att komma åt resurser i tjänsten för en fördefinierad tidsperiod.

Microsoft Azure SQL Database-gateway utför även förutom ovanstående:

- Säker kanal kapaciteten förhandlingar att implementera TDS FIPS 140-2 verifiera krypterade anslutningar när du ansluter till databasservrar.
- Tillståndskänsliga TDS-paketinspektion när acceptera anslutningar från klienter. Gatewayen validerar anslutningsinformationen och skickar vidare TDS-paket till rätt fysisk server baserat på det namn som anges i anslutningssträngen.

Den övergripande principen för nätverkssäkerhet för Microsoft Azure SQL Database-erbjudande är att endast tillåta anslutning och kommunikation som krävs för att tjänsten ska fungera. Alla andra portar, protokoll och anslutningar blockeras som standard. VLAN och ACL: er används för att begränsa nätverkskommunikation av källa och Målnätverk, protokoll och portnummer.

Godkända mekanismer för att implementera nätverksbaserade ACL: er omfattar: ACL: er på routrar och belastningsutjämnare. Dessa hanteras av Azure-nätverk, Gäst VM-brandväggen och Microsoft Azure SQL Database gateway brandväggsregler, som har konfigurerats av kunden.

## <a name="data-segregation-and-customer-isolation"></a>Isolering av data särskiljning och kunden
Azure-produktionsnätverket är strukturerad så att offentligt tillgänglig systemkomponenter åtskild från interna resurser. Fysiska och logiska gränser finns mellan webbservrar som ger tillgång till offentliga Azure-portalen och den underliggande Azure virtuell infrastruktur där kunden programinstanser och kundinformation finns.

All information som är offentligt tillgänglig hanteras i Azure-produktionsnätverket. Produktionsnätverket lyder tvåfaktorsautentisering och gräns skyddet, använder funktionen brandvägg och säkerhet som beskrivs i föregående avsnitt och använder data isolering funktioner som anges nedan.

### <a name="unauthorized-systems-and-isolation-of-fc"></a>Obehörig system och isolering av FC
Eftersom FC är Microsoft Azure Fabric centrala orchestrator, är betydande kontroller för att minimera hot, särskilt från potentiellt komprometterade FAs i kundprogram. FC kan inte identifiera all maskinvara vars enhetsinformation (till exempel MAC-adress) inte redan har lästs in i FC. DHCP-servrar i FC har konfigurerat en lista över MAC-adresser till de noder som de är beredda att starta. Även om obehöriga datorer är anslutna de inte ingår i Fabric-lager, och därför inte ansluten eller auktoriserad att kommunicera med alla system i Fabric-lagret. Detta minskar risken för obehörigt system kommunicerar med FC och få åtkomst till VLAN- och Azure.

### <a name="vlan-isolation"></a>VLAN-isolering
Azure-produktionsnätverket är logiskt indelade i tre primära VLAN:

- Det huvudsakliga VLANET – anslutningar obetrodda kunden noder
- FC-VLAN – innehåller betrodda FCs och ge support för system
- Enheten VLAN – innehåller betrott nätverk och andra infrastruktur-enheter

### <a name="packet-filtering"></a>Filtrering av nätverkspaket
IPFilter och programvara brandväggarna implementerats på rot-OS- och Gästoperativsystem noder framtvinga restriktioner för anslutningen och förhindrar att otillåten trafik mellan virtuella datorer.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor-programmet, roten OS och Gäst-VM
Isolering av roten OS från gäst-VM och Gäst-VM från varandra hanteras av hypervisor-programmet och rot-OS.

### <a name="types-of-rules-on-firewalls"></a>Typer av regler för brandväggar
En regel definieras som:

{Security Response Center (Src) IP, källport, mål IP målport mål protokollet In/ut Stateful tillståndslösa och tillståndskänsliga flödet Timeout}.

SYN paket tillåts in eller ut endast om någon av reglerna tillåter. För TCP använder Microsoft Azure tillståndslös regler där principen är att den endast kan alla icke-SYN paket till eller från den virtuella datorn. Säkerhet lokala är att alla värden stacken är flexibel ignoreras icke-SYN om SYN-paket inte påträffats tidigare. I kombination med tillståndslös SYNbased regeln ger en övergripande beteendet för en tillståndskänslig implementering av TCP-protokoll är tillståndskänslig.

För User Datagram Protocol (UDP) i Microsoft Azure använder en tillståndskänslig regel. Varje gång ett UDP-paket matchar en regel, skapas en omvänd flöde i omvänd ordning. Detta flöde har en inbyggd tidsgräns.

Kunder ansvarar för att konfigurera egna brandväggar på Microsoft Azure tillhandahåller. Här kan kunder definiera regler för inkommande och utgående trafik.

### <a name="production-configuration-management"></a>Produktion konfigurationshantering
Säker standardkonfigurationer underhålls av respektive team i Azure och Microsoft Azure SQL Database. Alla konfigurationsändringar till produktionssystem dokumenteras och följs via en central spårningssystemet. Programvara och maskinvara ändringarna spåras via central spårningssystemet. Nätverksändringar som rör ACL spåras med ACL Management Service (AMS).

Alla konfigurationsändringar till Microsoft Azure utvecklas och testas i mellanlagringsmiljön; och därefter distribuerade i produktionsmiljö. Programversioner granskas som en del av testning. Säkerhet och sekretess kontrollerar granskas som en del av checklista villkor. Ändringar har distribuerats på schemalagda intervall av respektive distributionsgruppen. Versioner granskas och signeras av respektive distribution team personal innan de distribueras till produktionen.

Ändringar övervakas för att lyckas. Ändringen är föras tillbaka till dess tidigare tillstånd eller en snabbkorrigering distribueras för att åtgärda felet med godkännande för den avsedda personalen på ett felscenario. Källa anläggningen, Git, TFS, MDS, utlöpare, Azure Security övervakning (ASM), FC och WinFabric plattform används att centralt hantera, tillämpa och verifiera konfigurationsinställningarna i Azure-virtuella miljön.

På samma sätt maskinvaru- och ändringar som har upprättat validering steg för att utvärdera sin anslutning till build-krav. Versionerna granskas och behörighet via en samordnad ändra Advisory Board (CAB-fil) i respektive grupper i stacken.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
