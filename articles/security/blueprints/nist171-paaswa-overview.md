---
title: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS-webbprogram för NIST SP 800-171
description: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 4d635d04c5207b3668bd179cec4f21e26dd59452
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946683"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Handlingsplan för säkerhet och efterlevnad i Azure-PaaS webb program för NIST särskild publikation 800-171

## <a name="overview"></a>Översikt
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller rikt linjer för att skydda sekretessbelagda oklassificerade uppgifter (Cui) som finns i icke-federala informations system och organisationer. NIST SP 800-171 fastställer 14 familjer av säkerhets krav för att skydda konfidentialiteten hos CUI.

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för att hjälpa kunder att distribuera en plattform som tjänst (PaaS)-webbapp i Azure som implementerar en delmängd av NIST SP 800-171-kontroller. Den här lösningen visar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad. Den fungerar också som grund för kunderna att bygga och konfigurera egna webb program i Azure.

Den här referens arkitekturen, tillhör ande implementerings guide och hot modell är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav. De bör inte användas som de är i en produktions miljö. Att distribuera den här arkitekturen utan modifiering är inte tillräckligt för att uppfylla kraven i NIST SP 800-171. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här Handlingsplan för säkerhet och efterlevnad i Azure tillhandahåller en referens arkitektur för ett PaaS-webbprogram med en Azure SQL Database Server del. Webb programmet finns i en isolerad App Service miljö, som är en privat, dedikerad miljö i ett Azure-datacenter. Miljö belastningen balanserar trafiken för webb programmet på virtuella datorer som hanteras av Azure. Den här arkitekturen omfattar även nätverks säkerhets grupper (NSG: er), en Azure Application Gateway, Azure DNS och Azure Load Balancer.

För förbättrad analys och rapportering kan Azure SQL-databaser konfigureras med columnstore-index. Azure SQL-databaser kan skalas upp eller ned eller stängas av helt som svar på kund användning. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda data Center för återhämtning. Geo-redundant lagring garanterar att data replikeras till ett sekundärt Data Center hundratals mil och lagras igen som tre kopior i data centret. Den här överenskommelsen förhindrar en negativ händelse på kundens primära Data Center från att resultera i förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory (RBAC-baserad åtkomst kontroll i Azure AD) används för att styra åtkomsten till distribuerade resurser. Dessa resurser innehåller kund nycklar i Azure Key Vault. System hälsan övervakas via Azure Monitor. Kunder konfigurerar övervaknings tjänsten att avbilda loggar. System hälsan visas på en enda instrument panel som är lätt att använda.

SQL Database hanteras ofta via SQL Server Management Studio. Den körs från en lokal dator som är konfigurerad för att få åtkomst till SQL-databasen via en säker VPN-eller Azure ExpressRoute-anslutning.

Application Insights ger hantering av program prestanda i real tid och genom Azure Monitor loggar *Microsoft rekommenderar vi att du konfigurerar en VPN-eller ExpressRoute-anslutning för hantering och data import i referens arkitekturen undernät.*

![PaaS webb program för NIST SP 800-171-referens arkitektur diagram](images/nist171-paaswa-architecture.png "PaaS webb program för NIST SP 800-171-referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Azure virtuella maskiner
    - (1) hantering/skydds (Windows Server 2016 Data Center)
- Azure Virtual Network
    - (1)/16 nätverk
    - (4)/24 nätverk
    - (4) nätverks säkerhets grupper
- Azure Application Gateway
    - Brandvägg för webbappar
        - Brand Väggs läge: skydd
        - Regel uppsättning: OWASP
        - Lyssnar port: 443
- Application Insights
- Azure Active Directory
- App Service-miljön v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (loggar)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure Resource Manager**: [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan användas av kunder för att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, koordinerad åtgärd. Kunder använder en mall för distribution. Mallen kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings-och taggnings funktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skydds-värd**: Skydds-värden är den enda post adressen som användarna kan använda för att få åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i NSG.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure-diagnostik-tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av VM-resurser när de inte används.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) är aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs.

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) är en Azure App Service-funktion. Kunder kan använda den för att bygga och vara värd för webb program i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Den erbjuder automatisk skalning och hög tillgänglighet. Det stöder Windows och Linux och möjliggör automatiserade distributioner från GitHub, Azure DevOps eller git lagrings platsen.

**App Service Environment**: [App Service-miljön](https://docs.microsoft.com/azure/app-service/environment/intro) är en app service-funktion. Det ger en helt isolerad och dedikerad miljö för säker körning av App Service program i stor skala.

App Services miljön är isolerad för att endast köra ett enda program. Den distribueras alltid till ett virtuellt nätverk. På grund av isolerings funktionen har referens arkitekturen fullständig klient isolering och tas bort från Azures miljö för flera innehavare. Kunderna har detaljerad kontroll över både inkommande och utgående program nätverks trafik. Program kan upprätta snabba säkra anslutningar över virtuella nätverk till lokala företags resurser. Kunder kan skala automatiskt med App Service-miljön baserat på inläsnings mått, tillgänglig budget eller ett definierat schema.

Användning av App Service-miljön för den här arkitekturen ger följande kontroller och konfigurationer:

- Värd i ett säkert säkerhets regler för virtuella Azure-nätverk och nätverks säkerhet.
- Självsignerat internt belastnings Utjämnings certifikat för HTTPS-kommunikation. Som bästa praxis rekommenderar Microsoft att du använder en betrodd certifikat utfärdare för förbättrad säkerhet.
- [Läge för intern belastnings utjämning](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (läge 3).
- Inaktivera [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Ändra [TLS-chiffer](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Kontrol lera [inkommande trafiks N/W-portar](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).
- [Brand vägg för webbaserade program – begränsa data](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md).
- Tillåt [Azure SQL Database trafik](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md).

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: [NSG: er](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande NSG: er finns:
- En NSG för Application Gateway
- En NSG för App Service-miljön
- En NSG för SQL Database
- En NSG för skydds-värden

Varje NSG: er har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto.
  - Azure Monitor loggar är anslutna till [NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)-diagnostiken.

**Undernät**: Varje undernät är associerat med motsvarande NSG.

**Azure DNS**: Domain Name System (DNS) ansvarar för översättning (eller matchning av) en webbplats eller ett tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för DNS-domäner som tillhandahåller namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster genom att använda samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kan användas av kunder för att skala sina program och skapa hög tillgänglighet för tjänster. Load Balancer stöder inkommande och utgående scenarier. Den ger låg latens och högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azures Data Center som standard. Alla transaktioner som Azure Storage via Azure Portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att uppfylla kraven för krypterade data i vila använder [](https://azure.microsoft.com/services/storage/) all lagring [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Den här funktionen hjälper till att skydda och skydda data som stöd för organisationens säkerhets åtaganden och krav på efterlevnad som definieras av NIST SP 800-171.

**Azure Disk Encryption**: [Disk kryptering](../azure-security-disk-encryption-overview.md) använder BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Den utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar. Den innehåller säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som oformaterad text i databas systemet. När data kryptering har Aktiver ATS kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterad text data.
- [Dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till användare eller program utan privilegier. Den kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Dynamisk data maskning hjälper till att minska åtkomsten så att känsliga data inte avslutar databasen via obehörig åtkomst. *Kunderna ansvarar för att justera inställningarna för att följa deras databas schema.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure AD och innehåller användare som har åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i så här integrerar du [program med Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Databasens kolumn kryptering använder också Azure AD för att autentisera programmet för att SQL Database. Mer information finns i så här [skyddar du känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan användas av administratörer för att definiera detaljerade åtkomst behörigheter. Med den kan de endast bevilja den åtkomst mängd som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad åtkomst till Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till resurser och data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kan användas av kunder för att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure AD Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter. Den konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter. Den undersöker också misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och-tjänster. Följande Key Vault funktioner hjälper kunder att skydda data:
- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en maskin varu säkerhetsmodul – skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med RBAC.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Azure Security Center**: Med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Security Center också åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller en misstänkt aktivitet äger rum. Kunder kan använda [anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) för att definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Security Center innehåller prioriterade säkerhets aviseringar och incidenter. Security Center gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot. Incident hanterings team kan använda rapporterna när de undersöker och reparerar hot.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhets risker genom att använda en Programgateway med en brand vägg för webbaserade program konfigurerad och OWASP-regel uppsättningen aktive rad. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivera [SSL](../../application-gateway/create-ssl-portal.md)-avlastning.
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Brand vägg för webbaserade program](../../application-gateway/waf-overview.md) (skydds läge).
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0-regel uppsättning.
- Aktivera [](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)diagnostikloggning.
- [Anpassade hälso](../../application-gateway/quick-create-portal.md)avsökningar.
- [Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Security Center ger också ett ryktes system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, lagrings loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar, för att uppfylla de särskilda kraven.

**Azure Monitor loggar**: Loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in organiseras de i separata tabeller för varje datatyp inom Log Analytics arbets ytor. På så sätt kan alla data analyseras tillsammans, oavsett den ursprungliga källan. Security Center integreras med Azure Monitor-loggar. Kunder kan använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory utvärdering](../../azure-monitor/insights/ad-assessment.md): Active Directory hälso kontrolls lösningen utvärderar hälso-och hälso tillståndet för Server miljöer med jämna mellanrum. Den innehåller en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum. Den ger kunder en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution. Den rapporterar även hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från SQL Database. Kunder kan använda Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen för att enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Övervakaren](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar hanterings tjänst för program prestanda för webbutvecklare på flera plattformar. Application Insights identifierar prestanda avvikelser. Kunder kan använda den för att övervaka Live-webbprogrammet. Application Insights innehåller kraftfulla analys verktyg som hjälper kunder att diagnostisera problem och förstå vad användarna gör med sina appar. IT&#39;är utformat för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/nist171-paaswa-tm) eller finns här. Den här modellen kan hjälpa kunderna att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![PaaS-webbprogram för NIST SP 800-171 hot modell](images/nist171-paaswa-threat-model.png "PaaS-webbprogram för NIST SP 800-171 hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-NIST SP 800-171-kund ansvar](https://aka.ms/nist171-crm) visas alla säkerhets kontroller som krävs av NIST SP 800-171. Den här matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-NIST sp 800-171 PaaS för webb program kontroll](https://aka.ms/nist171-paaswa-cim) innehåller information om vilka NIST SP 800-171-kontroller som hanteras av arkitekturen för PaaS-webbprogram. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här PaaS referens arkitektur för webb program. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft ännu fler säkra anslutnings alternativ. ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. ExpressRoute-anslutningar ansluter direkt till kundens telekommunikations leverantör. Det innebär att data inte färdas över Internet och inte exponeras för den. Dessa anslutningar ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
