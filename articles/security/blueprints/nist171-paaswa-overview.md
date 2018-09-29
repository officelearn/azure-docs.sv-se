---
title: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för SP NIST 800-171
description: Azure-säkerhet och efterlevnad skissen - SP för PaaS Web Application NIST 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a073c0ddfe9f75984d2aa47e51d04c7217589dc5
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433862"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för NIST Specialpublicering 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera en plattform som en tjänst (PaaS)-webbprogram i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visas hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav. Det fungerar också som en grund för kunder att skapa och konfigurera sina egna webbprogram i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav. De bör inte användas som-är i en produktionsmiljö. Distribuera den här arkitekturen utan ändringar är tillräckligt för att uppfylla kraven för SP NIST 800-171 helt. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här Azure Blueprint för säkerhet och efterlevnad tillhandahåller en Referensarkitektur för ett PaaS-webbprogram med en Azure SQL Database-serverdel. Webbprogrammet finns i en isolerad App Service-miljö, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Den miljö belastningsutjämning trafik för webbprogrammet över virtuella datorer (VM) som hanteras av Azure. Den här arkitekturen omfattar också nätverkssäkerhetsgrupper (NSG), en Programgateway i Azure, Azure DNS och Azure Load Balancer.

Azure SQL-databaser kan konfigureras med columnstore-index för förbättrad analys och rapportering. Azure SQL-databaser kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda datacentret för återhämtning. GEO-redundant lagring garanterar att data replikeras till ett sekundärt Datacenter hundratals mil bort och lagras igen som tre kopior inom datacentret. Det här förhindrar att en incident på kundens primära datacenter vilket resulterar i förlust av data.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory (Azure AD) rollbaserad åtkomstkontroll (RBAC) används för att styra åtkomst till distribuerade resurser. Dessa resurser inkluderar Kundnycklar i Azure Key Vault. Systemhälsa övervakas via Azure Monitor. Kunder konfigurera den här övervakningstjänsten för att samla in loggar. Platssystemets hälsostatus visas i en enda instrumentpanel som är lätt att använda.

SQL-databas är ofta hanteras via SQL Server Management Studio. Den körs från en lokal dator som är konfigurerad för att komma åt SQL-databas via en säker VPN eller Azure ExpressRoute-anslutning.

Application Insights ger hantering av programprestanda i realtid och analytics via Azure Log Analytics. *Microsoft rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.*

![PaaS-webbprogram för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-paaswa-architecture.png "PaaS webbprogram för Arkitekturdiagram för SP NIST 800-171-referens")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - ((1) /16 nätverk
    - (4) /24 nätverk
    - (4) nätverkssäkerhetsgrupper
- Azure Application Gateway
    - Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP
        - Lyssningsport: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Azure Resource Manager**: [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan användas av kunder för att arbeta med resurser i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, samordnad åtgärd. Kunder som använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som användarna kan använda för att få åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd är en säker anslutning till distribuerade resurser eftersom endast fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i NSG: N för att tillåta trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägg för Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av Virtuella datorresurser som.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiveras så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs.

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) är en funktion i Azure App Service. Kunder kan använda den för att bygga och hantera webbprogram i programmeringsspråket helst utan att behöva hantera infrastrukturen. Den erbjuder automatisk skalning och hög tillgänglighet. Den har stöd för Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

**App Service Environment**: [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) är en funktion i App Service. Det ger en helt isolerad och dedikerad miljö för säker körning av App Service-program i hög skala.

App Service-miljö är isolerad till Kör bara ett enda program. Det distribueras alltid till ett virtuellt nätverk. På grund av funktionen användarisolering referensarkitekturen har slutförts klientisolering och tas den bort från Azures flerklientsmiljö. Kunderna har detaljerad kontroll över både inkommande och utgående programnätverkstrafik. Programmen kan upprätta säkra höghastighetsanslutningar över virtuella nätverk till lokala företagsresurser. Kunderna kan ”Autoskala” med App Service Environment baserat på inläsningsmåtten, tillgängliga budget eller ett definierat schema.

Användning av App Service Environment för den här arkitekturen ger följande kontroller och konfigurationer:

- Värd i ett skyddat Azure virtuellt nätverk och Nätverkssäkerhetsregler.
- Självsignerat intern Azure load balancer certifikat för HTTPS-kommunikation. Som bästa praxis rekommenderar Microsoft användningen av en betrodd certifikatutfärdare för förbättrad säkerhet.
- [Läge för intern belastningsutjämning](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (läge 3).
- Inaktivera [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Ändra [TLS-chiffer](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Kontrollen [inkommande trafik N/W portar](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Brandvägg för webbaserade program – begränsa data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Tillåt [Azure SQL Database trafik](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller individuella VM-nivå. Följande Nätverkssäkerhetsgrupper finns:
- En NSG för Application Gateway
- En NSG för App Service-miljö
- En NSG för SQL-databas
- En NSG för skyddsmiljö-värd

Varje NSG: erna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto.
  - Log Analytics är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

**Azure DNS**: Domain Name System (DNS) ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Azure-infrastrukturen. Som värd för domäner i Azure, kan användare hantera DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering som för andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kan användas av kunder för att skala programmen och skapa hög tillgänglighet för tjänster. Belastningsutjämnare har stöd för inkommande och utgående scenarier. Det ger mindre fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-tillämpningar.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: att uppfylla krav för krypterade data i vila, alla [Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Den här funktionen kan du skydda och skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av SP NIST 800-171.

**Azure Disk Encryption**: [diskkryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) använder BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Det fungerar i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger assurance som lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker. Det ger säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När datakryptering är aktiverat, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt oformaterad text data.
- [Dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till replikdata användare eller program. Det kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Dynamisk datamaskning hjälper till att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. *Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsofts multitenant molnbaserade katalog- och identity management-tjänsten. Alla användare för den här lösningen skapas i Azure AD och inkludera användare som har åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Läs mer om hur du [integrera program med Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kolumnen databaskryptering använder också Azure AD för att autentisera program till SQL-databas. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kan användas av administratörer för att definiera detaljerade behörigheter. Med den, kan de bevilja endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad åtkomst för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt resurser och data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) kan användas av kunder för att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure AD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella sårbarheter som påverkar organisationens identiteter. Konfigurerar den automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter. Det kan också undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault kan du skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvara security-modul-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Azure Security Center**: med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Security Center har även åtkomst till befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Security Center använder en mängd funktioner och meddela kunder om eventuella attacker som är inriktade på sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller misstänkt aktivitet inträffar. Kunder kan använda [anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Security Center tillhandahåller rangordnade säkerhetsaviseringar och incidenter. Security Center gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot. Incidenter team kan använda rapporterna när de undersöka och åtgärda hot.

**Azure Application Gateway**: arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med en brandvägg för webbaserade program som konfigurerats och OWASP regeluppsättningen aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge).
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning.
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Anpassade hälsotillståndsavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Security Center innehåller också ett rykte system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar att uppfylla deras specifika krav.

**Azure Log Analytics**: loggar konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp i Operations Management Suite-arbetsytor. På så sätt kan analyseras alla data tillsammans oavsett originalkälla. Security Center integrerar med Log Analytics. Kunder kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory-lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Det ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Tjänsten ger kunderna med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning. Här rapporteras även hur många agenter är inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från SQL-databas. Kunder kan använda Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösning lätt kan identifiera ändringar i miljön.

**Azure Monitor**: [övervakaren](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar prestanda management-tjänsten för webbutvecklare på flera plattformar. Application Insights identifierar prestandaavvikelser. Kunder kan använda den för att övervaka det aktiva webbprogrammet. Application Insights innehåller kraftfulla analysverktyg som hjälper kunder att diagnostisera problem och förstå vad användarna gör med appen. Den&#39;s som utformats för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-paaswa-tm) eller hittar du här. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i infrastrukturen för system när de gör ändringar.

![PaaS-webbprogram för SP NIST 800-171 hotmodell](images/nist171-paaswa-threat-model.png "PaaS webbprogram för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 PaaS Web Application Control implementering Matrix](https://aka.ms/nist171-paaswa-cim) innehåller information som på vilka SP NIST 800-171-kontroller med hjälp av PaaS-webbprogramarkitektur. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för PaaS web application. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom VPN-tunneln trafik passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat anslutningsalternativ för ännu mer säker. ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. ExpressRoute-anslutningar ansluta direkt till en kunds telekommunikation providern. Därför kan överföras inte via Internet data och visas inte. De här anslutningarna erbjuder mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
