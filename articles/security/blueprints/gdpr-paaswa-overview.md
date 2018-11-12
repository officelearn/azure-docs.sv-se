---
title: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för GDPR
description: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för GDPR
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 916b2177e6963ce51e644c2bcf9204cb1e110349
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236592"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för GDPR

## <a name="overview"></a>Översikt
Den allmänna Dataskyddsförordningen GDPR (Data Protection) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga dataöverträdelser och träna sekretess personal och andra anställda. GDPR ger enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera eller analysera personliga data. Dataskyddsförordningen inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU) eller att samla in och analysera data som EU invånare. Dataskyddsförordningen gäller oavsett var det finns en organisation.

Microsoft har utformat Azure med branschledande säkerhetsåtgärder och sekretesspolicyer för att skydda data i molnet, inklusive kategorierna av personuppgifter som identifieras av GDPR. Microsofts [avtalsvillkor](https://aka.ms/Online-Services-Terms) genomför Microsoft till kraven i processorer.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att distribuera en plattform som en tjänst (PaaS)-miljö som är lämplig för ett enkelt webbprogram mot Internet. Den här lösningen visar sätt där kunder kan uppfylla de specifika kraven för säkerhet och efterlevnad av Dataskyddsförordningen och fungerar som en grund för kunder att skapa och konfigurera sina egna program för PaaS webblösningar i Azure. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i sin resa till GDPR-efterlevnad:
1. Identifiera: Identifiera vilka personliga data finns och var den finns.
2. Hantera: Styr hur personlig data används och komma åt.
3. Skydda: Upprätta säkerhetskontroller för att förhindra, upptäcka och svara på säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och tränga in meddelanden.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett sätt som GDPR-kompatibel.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen tillhandahåller en Referensarkitektur för ett PaaS-webbprogram med en Azure SQL Database-serverdel. Webbprogrammet är värd för en isolerad Azure App Service Environment, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Den miljö belastningsutjämning trafik för webbprogrammet över datorer som hanteras av Azure. Den här arkitekturen omfattar också nätverkssäkerhetsgrupper, ett Application Gateway, Azure DNS och belastningsutjämnare. Azure Monitor är dessutom realtidsanalys av systemhälsan. **Azure rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![PaaS Web Applicaiton för GDPR-referens för Arkitekturdiagram](images/gdpr-paaswa-architecture.png?raw=true "PaaS Web Applicaiton för Arkitekturdiagram för GDPR-referens")

Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Active Directory (AAD)
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) WAF-Programgateway aktiverat
        - brandväggsläge: Dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssnare: port 443
- Azure-virtuellt nätverk
- nätverkssäkerhetsgrupper
- Azure DNS
- Azure Storage
- Azure Monitor
- Application Insights
- Azure Security Center
- App Service Environment v2
- Azure Load Balancer
- Azure-webbapp
- Azure Resource Manager

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ger kunder möjlighet att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, samordnad åtgärd. Kunder använder en mall för distributionen mallen kan användas för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper kunder att hantera sina resurser efter distributionen.

**App Service Environment v2**: den [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) är en funktion i App Service som innehåller en helt isolerad och dedikerad miljö för säker körning av App Service-program i hög skala.

Ase-miljöer är isolerade för att endast köra en enda kunds program och de distribueras alltid till ett virtuellt nätverk. Kunderna har detaljerad kontroll över både inkommande och utgående programnätverkstrafik och programmen kan upprätta säkra höghastighetsanslutningar över virtuella nätverk till lokala företagsresurser.

Användning av ase-miljöer för den här arkitekturen är tillåtna för följande kontroller/konfigurationer:

- Värd i ett skyddat virtuellt Azure-nätverk och Nätverkssäkerhetsregler
- ASE som konfigurerats med självsignerat ILB-certifikat för HTTPS-kommunikation
- [Läge för intern belastningsutjämning](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (läge 3)
- Inaktivera [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändra [TLS-chiffer](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrollen [inkommande trafik N/W portar](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [WAF – begränsa Data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Tillåt [Azure SQL Database-trafik](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) ger kunder möjlighet att bygga och agera värd för webbprogram i programmeringsspråket helst utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtual Network
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor (ACL) som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller individuella VM-nivå. Följande Nätverkssäkerhetsgrupper finns:
- 1 NSG för Application Gateway
- 1 NSG för App Service-miljö
- 1 NSG för Azure SQL Database

Var och en av NSG: erna har specifika portar och protokoll som är öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Log Analytics är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

**Azure DNS**: The Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Azure-infrastrukturen. Användare kan som värd för domäner i Azure, hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som för andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör att kunder kan skala programmen och skapa hög tillgänglighet för tjänster. Belastningsutjämnare har stöd för inkommande samt utgående scenarier, och ger med låg fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda personuppgifter som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av GDPR.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   Azure SQL-databasen är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger garantier att lagras personliga data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga personliga data aldrig ska visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av registrerade, eftersom den låter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogik för att förhindra att bearbetningen av som är associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att maskera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med identifiering av personliga data som är kvalificerade för GDPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder måste justerar DDM inställningarna för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till Azure SQL-databasen.
-   Det utförs autentiseringen till programmet med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt personliga data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information, till exempel personuppgifter.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter** lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom kommer Azure Security Center åt befintliga konfiguration av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Application Gateway** arkitekturen minskar risken för säkerhetsproblem med en Programgateway med Web Application Firewall (WAF) och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF-läge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure Monitor innehåller utförlig loggning av system- och användaraktivitet samt filsystemets hälsa. Den samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway.
- **Arkivera loggen**: alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning. Dessa loggar kan ansluta till Azure Log Analytics för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom kan ingår följande övervakningslösningar som en del av den här arkitekturen:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): med Azure Automation-lösningen lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Application Insights och Azure SQL Database.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): The säkerhet och granskning instrumentpanelen innehåller en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): uppdateringshanteringslösningen kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking): The Change Tracking solution gör att kunder enkelt kan identifiera ändringar i miljön.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i kunders Azure-resurser.

**Application Insights**
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare på flera plattformar. Application Insights identifierar prestandaavvikelser och kunder kan använda den för att övervaka det aktiva webbprogrammet. Den inkluderar kraftfulla analysverktyg att hjälpa kunder att diagnostisera problem och förstå vad användare faktiskt gör med appen. Det är utformat för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/gdprPaaSdfd) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![PaaS Web Applicaiton för GDPR hotmodell](images/gdpr-paaswa-threat-model.png?raw=true "PaaS Web Applicaiton för GDPR hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen – GDPR kundens ansvar matrisen](https://aka.ms/gdprCRM) visar en lista över styrenhet och processor ansvar för alla GDPR-artiklar. Observera att för Azure-tjänster en kund är vanligtvis domänkontrollanten och Microsoft fungerar som processorn.

Den [Azure säkerhet och efterlevnad skissen - GDPR PaaS Web Application implementering Matrix](https://aka.ms/gdprPaaSWeb) innehåller information som på vilka GDPR artiklar täcks av PaaS webbprogramarkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för PaaS web application. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
