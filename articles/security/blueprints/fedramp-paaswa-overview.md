---
title: Azure-säkerhet och efterlevnad modell - PaaS webbprogram för FedRAMP
description: Azure-säkerhet och efterlevnad modell - PaaS webbprogram för FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 20aa842fb8168bc28a388c817f4e4eedbdd63ebd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34728026"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure-säkerhet och efterlevnad utkast: PaaS webbprogram för FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk och auktorisering hanteringsprogram (FedRAMP)](https://www.fedramp.gov/) är ett Förenta staternas myndigheter hela program som ger en standardiserad metod för utvärdering av säkerheten, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure-säkerhet och efterlevnad utkast vägledning för hur man distribuerar en Microsoft Azure-plattform som en tjänst (PaaS)-arkitektur som hjälper till att implementera en delmängd av FedRAMP hög kontroller. Den här lösningen ger vägledning om distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt som kunder kan använda för att uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund för kunder att Skapa och konfigurera sina egna lösningar på Azure.

Den här referensen för arkitekturen, associerade kontrollen implementeringsguider och hot modeller är avsedda att fungera som en grund för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är tillräckligt för att helt uppfylla kraven för FedRAMP hög baslinjen. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som FedRAMP-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen ger en Referensarkitektur för ett PaaS webbprogram med en Azure SQL Database-serverdel. Webbprogrammet finns i en isolerad Azure Apptjänst-miljö, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Den miljö belastningsutjämning trafik för webbprogrammet över virtuella datorer som hanteras av Azure. Den här arkitekturen även nätverkssäkerhetsgrupper, en Programgateway, Azure DNS och belastningsutjämnare. Operations Management Suite är dessutom realtidsanalys med tillstånd och säkerhet. **Azure rekommenderar konfigurera VPN eller ExpressRoute-anslutningen för hanterings- och import till referens arkitektur undernät.**

![PaaS webbprogram för FedRAMP referens Arkitekturdiagram](images/fedramp-paaswa-architecture.png?raw=true) ”PaaS webbprogram för FedRAMP referens Arkitekturdiagram”

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Brandvägg för webbaserade program
        - brandväggsläge: förebyggande
        - Regelsamlingen: OWASP 3.0
        - lyssnare: port 443
- Azure-virtuellt nätverk
- Nätverkssäkerhetsgrupper
- Azure DNS
- Azure Storage
- Operations Management Suite
- Azure Monitor
- Apptjänst-miljö v2
- Azure Load Balancer
- Azure-webbapp
- Azure Resource Manager

## <a name="deployment-architecture"></a>Arkitektur för distribution
I följande avsnitt beskrivs distribution och implementeringen elementen.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ger kunder möjlighet att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, samordnad åtgärd. Kunder som använder en mall för distribution och mallen kan användas för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper kunder att hantera sina resurser efter distributionen.

**App-miljö v2**: den [Azure App Service miljö (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) är en funktion i App Service som tillhandahåller en helt isolerad och dedikerad miljö för Apptjänst program som körs på ett säkert sätt i hög skala.

ASEs är isolerad för att endast köra en enda kund program och alltid distribueras till ett virtuellt nätverk. Kunder har detaljerad kontroll över både inkommande och utgående nätverkstrafik och program kan upprätta säkra höghastighetsanslutning över virtuella nätverk till lokala företagsresurser.

Användning av ASEs för den här arkitekturen tillåts för följande kontroller/konfigurationer:

- Värden i en skyddad virtuella Azure-nätverket och nätverk säkerhetsregler
- ASE som konfigurerats med självsignerat ILB certifikat för HTTPS-kommunikation
- [Intern belastningsutjämning läge](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Inaktivera [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändra [TLS-chiffer](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrollen [inkommande trafik N-/ skrivåtkomst portar](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web Application Firewall – begränsa Data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Tillåt [Azure SQL Database-trafik](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

Den [vägledning och rekommendationer](#guidance-and-recommendations) avsnitt innehåller ytterligare information om ASEs.

**Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) ger kunder möjlighet att skapa och vara värd för webbprogram i programmeringsspråket valfri utan att hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Visual Studio Team Services eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtual Network
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller individuella VM-nivå. Det finns följande NSG: er:
- 1 NSG för Programgateway
- 1 NSG för Apptjänst-miljö
- 1 NSG för Azure SQL-databas

Var och en av de NSG: er har specifika portar och protokoll öppnas så att lösningen fungerar korrekt och på ett säkert sätt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) aktiveras och lagras i ett lagringskonto
  - OMS Log Analytics är ansluten till den [NSGS diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

**Azure DNS**: I Domain Name System eller DNS är översätter (eller lösa) en webbplats eller tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd-tjänst för DNS-domäner som tillhandahåller namnmatchning med hjälp av Azure-infrastrukturen. Användarna kan hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster genom att värdhantera domäner i Azure. Azure DNS stöder också privata DNS-domäner.

**Azure belastningsutjämnare**: [Azure belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) ger kunder möjlighet att skala sina program och skapa hög tillgänglighet för tjänster. Belastningsutjämnaren stöder inkommande samt utgående scenarier och ger hög genomströmning, låg svarstid och skalas upp till miljontals flöden för alla TCP och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal uppstå via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i viloläge med hjälp av kryptering, databasen granskning och andra åtgärder.

**Azure Storage**: att uppfylla krypterade data på övriga krav, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för datadiskar. Lösningen integreras med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

**Azure SQL Database**: I Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   Azure SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest.
-   [Regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Alltid krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga data aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering bara klientprogram eller programservrar med tillgång till nycklarna kan komma åt oformaterad text data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att avbryta bearbetningen.
- [SQL-databas dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter den referens för arkitekturen distribuerar. Kunderna måste du justera inställningar för att uppfylla sina databasschemat för dynamisk datamaskering.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker hanteringsfunktioner identitet i Azure-miljön:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till Azure SQL-databasen.
-   Tillämpningen utförs autentiseringen med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar exakt fokuserad åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumerationen och åtkomst till resurser kan begränsas baserat på användarrollen.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har tillgång till viss information.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att tillämpa på begäran, just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory-identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkt incidenter att vidta lämpliga åtgärder kan lösas.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter** lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder skydda data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på grundval av behov.
- Key Vault åtkomstprinciper definieras med minsta behörighet till nycklar och hemligheter.
- Alla nycklar och hemligheter i Nyckelvalvet ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade av särskilda maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostik-loggarna för Key Vault aktiveras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklarna är begränsade till de som krävs.

**Programgateway** arkitekturen minskar risken för säkerhetsproblem som använder en Programgateway med Brandvägg för webbaserade program och OWASP RuleSet-metod aktiverad. Ytterligare funktioner är:
- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS version 1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Förebyggande läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 RuleSet-metod
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="logging-and-auditing"></a>Granskning och loggning
Operations Management suite ger utförlig loggning av system- och användaraktivitet samt systemhälsa. Operations Management suite [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att bestämma en åtgärd initieraren tid på förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, Key Vault granskningsloggar och Programgateway loggar för åtkomst och brandväggen.
- **Arkivering av loggen**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar till bevarande organisation-specifika krav. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom kan ingår följande Operations Management suite lösningar som en del av denna arkitektur:
-   [Active directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks samla in loggar från Application Insights och Azure SQL Database.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

**Azure-Monitor**
[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användarna att spåra prestanda, upprätthålla säkerheten och identifiera trender genom att aktivera organisationer att granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i kundernas Azure-resurser.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/fedrampPaaSWebAppDFD) eller finns nedan. Den här modellen hjälper kunder att förstå punkterna i risken i infrastrukturen för system när ändringar görs.

![PaaS webbprogram för FedRAMP hotmodell](images/fedramp-paaswa-threat-model.png?raw=true "PaaS Web Flersvalsstart för FedRAMP hotmodell")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad
Den [Azure-säkerhet och efterlevnad modell - FedRAMP hög kunden ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen anger om genomförandet av varje kontroll ansvarar för Microsoft, kund, eller delas mellan två.

Den [Azure-säkerhet och efterlevnad modell - FedRAMP PaaS WebApp hög kontroll implementering matrisen](https://aka.ms/fedrampPaaSWebAppCIM) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen innehåller information som omfattas kontroller av PaaS web application arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN- och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för PaaS web application. Kunder kan genom på lämpligt sätt att skapa en VPN eller ExpressRoute, lägga till ett lager av dataskydd under överföring.

Genom att implementera en säker VPN-tunnel med Azure, kan du skapa en VPN-anslutning mellan ett lokalt nätverk och ett Azure Virtual Network. Den här anslutningen sker via Internet och ger kunder möjlighet att på ett säkert sätt ”tunnel” information i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism för.

Eftersom trafiken i VPN-tunnel passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutning. Azure ExpressRoute är en fast WAN länken mellan Azure och en lokal plats eller en värdleverantör för Exchange. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Dessutom eftersom det här är en direkt anslutning av kundens telekommunikation providern data överföras inte via Internet och därför visas inte för den.

Bästa praxis för att implementera en säker hybrid-nätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
