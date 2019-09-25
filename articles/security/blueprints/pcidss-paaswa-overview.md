---
title: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS webb program för PCI DSS
description: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS webb program för PCI DSS
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 45c3e5da6cff51e14d22ed3a6a3ab526b96b50ef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259654"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): PaaS-webbprogram för PCI DSS

## <a name="overview"></a>Översikt

Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation ger vägledning för distribution av ett betalnings kort bransch data säkerhets standarder (PCI DSS 3,2) kompatibel plattform som en tjänst (PaaS) som passar för insamling, lagring och hämtning av kort innehavare. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en gemensam referens arkitektur och demonstrerar hur kunder kan uppfylla specifika krav på säkerhet och efterlevnad och fungerar som en grund för kunder att bygga och Konfigurera egna lösningar på Azure. Lösningen implementerar en delmängd av kraven från PCI DSS 3,2. Mer information om kraven för PCI DSS 3,2 och den här lösningen finns i avsnittet [Compliance documentation](#compliance-documentation) .

Den här Handlingsplan för säkerhet och efterlevnad i Azure-Automation distribuerar automatiskt en PaaS-webbprograms referens arkitektur med förkonfigurerade säkerhets kontroller för att hjälpa kunderna att uppnå kompatibilitet med PCI DSS 3,2-krav. Lösningen består av Azure Resource Manager mallar och PowerShell-skript som hjälper till att distribuera och konfigurera resurser.

Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö. Det räcker inte att distribuera ett program i den här miljön utan modifiering för att uppfylla kraven i PCI DSS 3,2. Observera följande:
- Den här arkitekturen tillhandahåller en bas linje som hjälper kunder att använda Azure på ett PCI DSS 3,2-kompatibelt sätt.
- Kunderna ansvarar för att utföra lämpliga bedömningar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

Att uppnå PCI DSS-kompatibilitet kräver att en ackrediterad uppfyller kraven enligt (kvalificerad Security-bedömare) certifierar en produktions kund lösning. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

Klicka [här](https://aka.ms/pcidss-paaswa-repo) om du vill ha distributions anvisningar.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation distribuerar en referens arkitektur för ett PaaS-webbprogram med en Azure SQL Database Server del. Webb programmet finns i ett isolerat Azure App Service-miljön, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Miljö belastningen balanserar trafiken för webb programmet på virtuella datorer som hanteras av Azure. Den här arkitekturen omfattar även nätverks säkerhets grupper, en Application Gateway, Azure DNS och Load Balancer.

För förbättrad analys och rapportering kan Azure SQL-databaser konfigureras med columnstore-index. Azure SQL-databaser kan skalas upp eller ned eller stängas av helt som svar på kund användning. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda data Center för återhämtning. Geografiskt redundant lagring garanterar att data replikeras till ett sekundärt Data Center hundratals mil och lagras igen som tre kopior i data centret, vilket förhindrar en negativ händelse i kundens primära Data Center från att resultera i förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory rollbaserad åtkomst kontroll används för att kontrol lera åtkomsten till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. System hälsan övervakas via Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel.

Azure SQL Database hanteras ofta via SQL Server Management Studio, som körs från en lokal dator som har kon figurer ATS för att få åtkomst till Azure SQL Database via en säker VPN-eller ExpressRoute-anslutning.

Dessutom ger Application Insights prestanda hantering i real tid för program och analys genom Azure Monitor loggar. **Microsoft rekommenderar att du konfigurerar en VPN-eller ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.**

![PaaS webb program för PCI DSS referens arkitektur diagram](images/pcidss-paaswa-architecture.png "PaaS webb program för PCI DSS referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- App Service-miljön v2
- Application Gateway
  - (1) brand vägg för webbaserade program
    - Brand Väggs läge: skydd
    - Regel uppsättning: OWASP 3,0
    - Lyssnar port: 443
- Application Insights
- Azure Active Directory
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 nätverk
    - (4)/24 nätverk
    - (4) nätverks säkerhets grupper
- Azure-webbapp

## <a name="deployment-architecture"></a>Distributions arkitektur

I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) gör det möjligt för kunderna att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, koordinerad åtgärd. Kunder använder en mall för distribution och mallen kan fungera för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings-och taggnings funktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger användare åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärran sluten trafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure-diagnostik tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av virtuella dator resurser när den inte används
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs

**App Service-miljön v2**: Azure App Service-miljön är en App Service-funktion som ger en helt isolerad och dedikerad miljö för säker körning av App Service program i stor skala. Den här isolerings funktionen krävs för att uppfylla kraven på PCI-efterlevnad.

App Service miljöer är isolerade för att bara köra en enskild kunds program och distribueras alltid till ett virtuellt nätverk. Den här isolerings funktionen gör att referens arkitekturen har fullständig klient isolering och tar bort den från Azures miljö för flera innehavare, vilket förhindrar att flera klienter räknar upp de distribuerade App Service-miljön resurserna. Kunderna har detaljerad kontroll över både inkommande och utgående program nätverks trafik, och program kan upprätta snabba säkra anslutningar över virtuella nätverk till lokala företags resurser. Kunder kan skala automatiskt med App Service-miljön baserat på inläsnings mått, tillgänglig budget eller ett definierat schema.

Använd App Service-miljöer för följande kontroller och konfigurationer:

- Värd i en skyddad Azure-Virtual Network och nätverks säkerhets regler
- Självsignerat ILB-certifikat för HTTPS-kommunikation
- [Läge för intern belastnings utjämning](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Inaktivera [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Ändra [TLS-chiffer](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrol lera [inkommande trafiks N/W-portar](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Brand vägg för webbaserade program – begränsa data](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Tillåt [Azure SQL Database trafik](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Azure-webbapp**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) gör det möjligt för kunder att bygga och vara värd för webb program i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtuellt nätverk

Arkitekturen definierar ett privat Virtual Network med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller Access Control listor (ACL: er) som tillåter eller nekar trafik inom en Virtual Network. Nätverks säkerhets grupper kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande nätverks säkerhets grupper finns:

- 1 nätverks säkerhets grupp för Application Gateway
- 1 nätverks säkerhets grupp för App Service-miljön
- 1 nätverks säkerhets grupp för Azure SQL Database
- 1 nätverks säkerhets grupp för skydds-värd

Varje nätverks säkerhets grupp har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverks säkerhets grupp:

- [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
- Azure Monitor loggar är anslutna till [nätverks säkerhets gruppens&#39;diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerat med motsvarande nätverks säkerhets grupp.

**Azure DNS**: Domain Name System eller DNS ansvarar för översättning (eller matchning) av en webbplats eller ett tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för DNS-domäner som ger namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör det möjligt för kunderna att skala sina program och skapa hög tillgänglighet för tjänster. Load Balancer stöder inkommande och utgående scenarier och ger låg latens, högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

### <a name="data-in-transit"></a>Data under överföring

Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner som Azure Storage via Azure Portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Detta hjälper till att skydda och skydda kort korts data i stöd för organisatoriska säkerhets åtaganden och krav på efterlevnad som definieras av PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:

- [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
- [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
- Azure SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte omfattas av obehörig åtkomst.
- [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
- [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till icke-privilegierade användare eller program. Dynamisk data maskning kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta hjälper till att identifiera och minska åtkomsten till data så att den inte avslutar databasen via obehörig åtkomst. Kunderna ansvarar för att justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker tillhandahåller funktioner för att hantera åtkomst till kort innehavares data i Azure-miljön:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsoft&#39;s molnbaserad, molnbaserad katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som har åtkomst till Azure SQL Database.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure Active Directory för att autentisera programmet att Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Med [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data för kort innehavare. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information, till exempel kort korts data. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en&#39;organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som är&#39;relaterade till en organisations identiteter. undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet

**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda och komma åt sådana data:

- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med rollbaserad åtkomst kontroll.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhets problem med en Azure Application Gateway med en brand vägg för webbaserade program konfigurerad, och OWASP-ruleset har Aktiver ATS. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](../../application-gateway/create-ssl-portal.md)
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brand vägg för webbaserade program](../../application-gateway/waf-overview.md) (skydds läge)
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0 ruleset
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälso avsökningar](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp inom Log Analytics arbets ytor, vilket innebär att alla data kan analyseras tillsammans oavsett den ursprungliga källan. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](../../azure-monitor/insights/ad-assessment.md): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från Azure SQL Database. Med Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen kan kunder enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar hanterings tjänst för program prestanda för webbutvecklare på flera plattformar. Application Insights identifierar prestanda avvikelser och kunder kan använda den för att övervaka Live-webbprogrammet. Den innehåller kraftfulla analys verktyg som hjälper kunder att diagnostisera problem och förstå vad användare faktiskt gör med sina appar. IT&#39;är utformat för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/pcidss-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![PaaS-webbprogram för PCI DSS hot modell](images/pcidss-paaswa-threat-model.png "PaaS-webbprogram för PCI DSS hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – PCI DSS kund ansvar](https://aka.ms/pcidss-crm) visas styrkort och processor ansvar för alla PCI DSS 3,2-krav.

[Matrisen handlingsplan för säkerhet och efterlevnad i Azure – PCI DSS PaaS Web Application implementation](https://aka.ms/pcidss-paaswa-cim) innehåller information om vilka PCI DSS 3,2-krav som hanteras av PaaS-webbappens arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje artikel som omfattas.

## <a name="deploy-this-solution"></a>Distribuera den här lösningen
Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Managers API-tjänst för att distribuera resurser i Azure. Detaljerade distributions anvisningar finns [här](https://aka.ms/pcidss-paaswa-repo).

#### <a name="quickstart"></a>Snabbstart
1. Klona eller ladda ned [den här](https://aka.ms/pcidss-paaswa-repo) GitHub-lagringsplatsen till din lokala arbets Station.

2. Granska 0-Setup-AdministrativeAccountAndPermission.md och kör de tillhandahållna kommandona.

3. Distribuera en test lösning med contoso-exempel data eller pilot en inledande produktions miljö.
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - Det här skriptet distribuerar Azure-resurser för en demonstration av en WebStore med exempel data från contoso.
   - 1-DeployAndConfigureAzureResources.ps1
     - Det här skriptet distribuerar de Azure-resurser som behövs för att stödja en produktions miljö för ett kundägda webb program. Den här miljön bör anpassas ytterligare av kunden utifrån organisationens krav.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute

En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här PaaS-webbprogrammets referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att &quot;på&quot; ett säkert sätt dirigera information i en krypterad länk mellan kundens&#39;nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens&#39;teleoperatörs leverantör, överförs inte data via Internet och visas därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Det här dokumentet tillhandahålls &quot;i befintligt skick.&quot; Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
- Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
- Kunder får kopiera och använda det här dokumentet för intern referens.
- Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kund&#39;s Azure-licens eller prenumerations kostnader.
- Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
- Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
