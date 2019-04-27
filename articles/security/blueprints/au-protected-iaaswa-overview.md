---
title: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för Australien SKYDDAS
description: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för Australien SKYDDAS
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 3c82a88ea15b52672f9bed428e2e7af40a65309c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610212"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för Australien skyddas

## <a name="overview"></a>Översikt
Innehåller riktlinjer för distribution av en infrastruktur som en tjänst (IaaS)-miljö som är lämplig för insamling, lagring och hämtning av AU-skyddade government data som är kompatibla med målen för den här Azure Blueprint för säkerhet och efterlevnad den australiensiska Government Information Security manuell (ISM) skapas genom att Australian Signals Directorate (ASD). Den här skissen presenterar en gemensam Referensarkitektur och hjälper till att demonstrera hanterar känsliga government data i en miljö för säker, kompatibel och flera nivåer.

Den här referensarkitekturen och Implementeringsguide för hotmodell ger en grund för kunder att genomföra sina egna planerings- och system ackrediteringsprocesser att hjälpa kunder att distribuera arbetsbelastningar till Azure på ett sätt som ASD-kompatibel. Kunder kan välja att implementera en Azure VPN Gateway eller ExpressRoute att använda externa tjänster och integrera lokala resurser med Azure-resurser. Kunder måste ta hänsyn till säkerhetsriskerna med hjälp av lokala resurser. Ytterligare konfiguration krävs för att uppfylla alla krav, eftersom de kan variera beroende på specifika för varje kund-implementering.

För att uppnå ASD-kompatibilitet krävs att en Information Security registrerad bedömare granskar systemet. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen används en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Application Gateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data är konfigurerade i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en Always On-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och Active Directory-grupprinciper används för att tillämpa säkerhets- och konfigurationer på driftsystemnivå. En skyddsmiljö-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser.

Arkitekturen kan leverera en säker hybridmiljö som utökar ett lokalt nätverk till Azure, vilket gör att web-baserade arbetsbelastningar som kan nås på ett säkert sätt av företagets användare av en organisations privat lokalt nätverk eller från internet. Kunden är ansvariga för såväl som ansvarar för alla aspekter av säkerhet, åtgärder och efterlevnad för lokala lösningar.

Azure-resurser i den här lösningen kan ansluta till ett lokalt nätverk eller Datacenter delade miljö (t.ex. CDC i Canberra) via IPSec VPN med hjälp av Azure VPN Gateway eller ExpressRoute... Beslutet att använda en VPN-anslutning ska göras med klassificeringen av överförda data och nätverkssökvägen i åtanke. Kunder som kör storskaliga, bör verksamhetskritiska arbetsbelastningar med stordatakrav en hybridnätverksarkitektur med ExpressRoute för privat nätverksanslutning till Azure-tjänster. Referera till avsnittet vägledning och rekommendationer finns mer information om anslutningsmekanismerna till Azure.

Federation med Azure Active Directory ska användas för att användarna kan autentiseras med lokala autentiseringsuppgifter och få åtkomst till alla resurser i molnet med hjälp av en lokal Active Directory Federation Services-infrastruktur. Active Directory Federation Services kan ge förenklad, säkrad federation och enkel inloggning för funktioner för Identitetshantering för den här hybridmiljö. Referera till avsnittet vägledning och rekommendationer för ytterligare information om Azure Active Directory-installation.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda regionen för återhämtning. Azure-regioner har distribuerats i elastiska regionpar och geografiskt redundant lagring säkerställer att data replikeras till den andra regionen med samt tre kopior. Detta förhindrar att en incident på kundens primära platsen, vilket resulterar i förlust av data.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser och nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel. Azure Application Gateway har konfigurerats som en brandvägg i förhindringsläge och kräver TLS-trafik med lägst version 1.2.

![IaaS-webbprogram för AU-skyddade Referensarkitektur](images/au-protected-iaaswa-architecture.png?raw=true "IaaS-webbprogram för Arkitekturdiagram för AU-skyddade referens") 

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Tillgänglighetsuppsättningar
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssningsport: 443
- Azure Cloud Witness
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Monitor-loggar
- Azure Storage
- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) nätverkssäkerhetsgrupper
    - Azure Network Watcher
- Recovery Services Vault

Den här skissen innehåller Azure-tjänster som inte har certifierats för användning i den skyddade klassificeringen av australiensiska Cyberhot Security Center (ACSC). Alla tjänster som ingår i denna Referensarkitektur har certifierats av ACSC på nivån spridning begränsar markörer (DLM). Microsoft rekommenderar att kunder läsa publicerade säkerheten och granskningsrapporter relaterade till dessa Azure-tjänster och använda deras riskhanteringsramverket för att avgöra om Azure-tjänsten är lämplig för deras interna ackreditering och användning till den Skyddade klassificering.

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: Den här lösningen används resurser i en arkitektur med ett separat undernät, databas-undernät, Active Directory-undernät och hanteringsundernätet i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på de enskilda undernäten att begränsa trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns i konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera nätverkssäkerhetsgrupper genom att redigera filen ovan med [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en särskild grupp:
- 1 nätverkssäkerhetsgrupp för Application Gateway (LBNSG)
- 1 nätverkssäkerhetsgruppen för skyddsmiljö-värd (MGTNSG)
- 1 nätverkssäkerhetsgruppen för SQL-servrar och molnet vittne (SQLNSG)
- 1 nätverkssäkerhetsgruppen för webbnivån (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. 

Arkitekturen använder för skyddade data under överföring från kundägda nätverk, Internet eller ExpressRoute med en VPN-Gateway som konfigurerats med IPSEC.

Dessutom sker alla transaktioner till Azure via Azure-hanteringsportalen via HTTPS som använder TLS 1.2.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: Att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av Australian Government ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**SQL Server**: SQL Server-instansen använder följande säkerhetsåtgärder för databasen:
-   [Granskning av SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) spårar databashändelser och skriver dem om du vill granska loggar.
-   [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [Krypterade kolumner](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. **Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.**

### <a name="identity-management"></a>Identitetshantering
Kunder kan använda en lokal Active Directory Federation Services att federera med [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), vilket är Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integrerar lokala kataloger med Azure Active Directory. Alla användare i den här lösningen kräver Azure Active Directory-konton. Med federation inloggning, kan användare logga in på Azure Active Directory och autentisera till Azure-resurser med lokala autentiseringsuppgifter.

Dessutom är följande funktioner i Azure Active Directory hjälper dig att hantera åtkomst till data i Azure-miljön:
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad behörighet för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisation&#39;s identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisation&#39;s identiteter , och undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

**Azure Multi-Factor Authentication**: Multifaktorautentisering bör implementeras för att skydda identiteter. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) är en lättanvänd, skalbar och tillförlitlig lösning som ger ett annat sätt för att skydda användare. Azure Multi-Factor Authentication använder kraften i molnet och integreras med en lokal Active Directory och anpassade program. Det här skyddet utökas till verksamhetskritiska scenarion med stora volymer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda och komma åt dessa data:

- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvarusäkerhetsmodul skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS VM diskkrypteringsnycklar och hemligheter.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

Dessutom är den här referensarkitekturen använder den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Azure Security Center. När du konfigurerat rapporterar en partner-agenten (t.ex. Qualys) sårbarhetsdata till partnerns hanteringsplattform. I sin tur innehåller partnerns hanteringsplattform säkerhetsproblem och hälsoövervakningsdata tillbaka till Azure Security Center, så att kunder kan snabbt identifiera sårbara virtuella datorer.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhetsproblem med Azure Application Gateway med en brandvägg för webbaserade program som konfigurerats och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Lösningen har distribuerats alla virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, att aktivera återställningen går snabbare.

**Molnet vittne**: [Molnet vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Azure Monitor-loggar**: Dessa loggar konsolideras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp, som låter alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Azure Monitor-loggar så att kunder kan använda Kusto-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Azure [övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Server. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure-resurser.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): I Azure Network Watcher finns verktyg för att övervaka, diagnostisera, visa mått samt aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk.  Samväldet entiteter bör implementera Network Watcher flödesloggar för NSG: er och virtuella datorer. Dessa loggar bör lagras på en dedikerad lagringskonto som endast säkerhetsloggar lagras i och åtkomst till storage-konto bör skyddas med rollen baserade åtkomstkontroller.

## <a name="threat-model"></a>Hotmodell
Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/au-protected-iaaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![IaaS-webbprogram för AU-skyddade Hotmodell](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS-webbprogram för AU-skyddade Threat modelldiagram")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den här dokumentationen för efterlevnad skapas av Microsoft baserat på plattformar och tjänster från Microsoft. På grund av det breda utbudet av kunddistributioner tillhandahåller den här dokumentationen en generaliserad för en lösning som endast finns i Azure-miljön. Kunder kan identifiera och använda andra produkter och tjänster baserat på sina egna miljöer och affärsresultat. Kunder som vill använda lokala resurser måste uppfylla säkerhets- och åtgärder för dessa lokala resurser. Dokumenterade lösningen kan anpassas av kunder att lösa sina specifika lokala och säkerhetskrav.

Den [Azure säkerhet och efterlevnad skissen – AU-PROTECTED kundens ansvar matrisen](https://aka.ms/au-protected-crm) visar en lista över alla säkerhetskontroller som krävs av AU-skyddade. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen – AU-PROTECTED IaaS Web Application implementering Matrix](https://aka.ms/au-protected-iaaswa-cim) innehåller information som AU-skyddade kontroller med hjälp av IaaS webbprogramarkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker tunnel med IPSec VPN måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för IaaS web application för sekretessbelagda uppgifter. Kunder kan genom att korrekt ställa in ett IPSec VPN, lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker IPSec VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen kan ske via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. 

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett alternativ för privat anslutning. Azure ExpressRoute är en dedikerad länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider och betraktas som ett privat nätverk. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter och kortare svarstider än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

För att skydda sekretessbelagda data, oavsett om du använder Internet eller Azure ExpressRoute, måste kunder konfigurera sina IPSec-VPN genom att använda följande inställningar:

• För kundens VPN-initieraren måste konfigureras för en SA-livstid inte är större än 14400 sekunder.
• För kundens VPN-initieraren måste inaktivera IKEv1 aggressivt läge.
• För kundens VPN-initieraren måste konfigurera Perfect Forward Secrecy.
• Kunden VPN-initierare måste konfigurera användningen av HMAC-SHA256 eller större.

Konfigurationsalternativen för VPN-enheter och IPSec / IKE-parametrar är [tillgängliga](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) för granskning.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Dessutom [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) låter kunder konfigurera federation med en lokal [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) och Azure Active Directory. Med federation inloggning, kan kunderna ge användare att logga in på Azure Active Directory-baserade tjänster med sina lokala lösenord och utan att behöva ange sina lösenord igen i företagsnätverket. Du kan distribuera en ny installation av Active Directory Federation Services med hjälp av alternativet federation med Active Directory Federation Services, eller du kan ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

För att förhindra klassificerade data synkroniseras till Azure Active Directory, kan kunder begränsa de attribut som replikeras till Azure Active Directory genom att använda följande inställningar i Azure Active Directory Connect:
- [Aktivera filtrering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Inaktivera synkronisering av lösenordshash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Inaktivera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Inaktivera tillbakaskrivning av enhet](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Lämna standardinställningarna för [förhindra oavsiktliga borttagningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) och [automatisk uppgradering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Ansvarsfriskrivning
- Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
- Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
- Kunderna får kopiera och använda det här dokumentet som intern referens.
- Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
- Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
- Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
