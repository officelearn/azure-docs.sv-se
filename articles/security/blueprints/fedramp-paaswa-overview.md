---
title: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS-webbprogram för FedRAMP
description: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS-webbprogram för FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: e8458d505575a5bf39bbd0a9970c5044b9cbd0f0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257326"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): PaaS-webbprogram för FedRAMP

## <a name="overview"></a>Översikt

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) är ett USA myndighets omfattande program som ger en standardiserad metod för säkerhetsbedömning, auktorisering och kontinuerlig övervakning av moln produkter och tjänster. Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning om hur du levererar en Microsoft Azure plattform som en tjänst (PaaS) som hjälper till att implementera en delmängd av FedRAMP-hög kontroller. Den här lösningen ger vägledning om distribution och konfiguration av Azure-resurser för en gemensam referens arkitektur som demonstrerar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad och fungerar som en grund för kunderna att Bygg och konfigurera egna lösningar på Azure.

Den här referens arkitekturen, de tillhör ande guiderna för kontroll implementering och hot modeller är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav och bör inte användas i en produktions miljö. Det räcker inte att distribuera ett program i den här miljön utan att ändra kraven i FedRAMP hög bas linje. Observera följande:
- Arkitekturen ger till gång till en bas linje som hjälper kunder att distribuera arbets belastningar till Azure på ett FedRAMP-kompatibelt sätt.
- Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här lösningen ger en referens arkitektur för ett PaaS-webbprogram med en Azure SQL Database Server del. Webb programmet finns i ett isolerat Azure App Service-miljön, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Miljö belastningen balanserar trafiken för webb programmet mellan virtuella datorer som hanteras av Azure. Den här arkitekturen omfattar även nätverks säkerhets grupper, en Application Gateway, Azure DNS och Load Balancer. Dessutom ger Azure Monitor real tids analys av system hälsan. **Azure rekommenderar att du konfigurerar en VPN-eller ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.**

![PaaS webb program för FedRAMP referens arkitektur diagram](images/fedramp-paaswa-architecture.png?raw=true "PaaS webb program för FedRAMP referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) brand vägg för webbaserade program
        - Brand Väggs läge: skydd
        - Regel uppsättning: OWASP 3,0
        - Lyssnare: port 443
- Virtuellt Azure-nätverk
- Nätverkssäkerhetsgrupper
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service-miljön v2
- Azure Load Balancer
- Azure-webbapp
- Azure Resource Manager

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) gör det möjligt för kunderna att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, koordinerad åtgärd. Kunder använder en mall för distribution och mallen kan fungera för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings-och taggnings funktioner som hjälper kunder att hantera sina resurser efter distributionen.

**App Service-miljön v2**: [Azure App Service-miljön (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) är en app service funktion som ger en helt isolerad och dedikerad miljö för säker körning av App Service program i hög skala.

ASE är isolerade för att bara köra en enskild kunds program och distribueras alltid till ett virtuellt nätverk. Kunderna har detaljerad kontroll över både inkommande och utgående program nätverks trafik, och program kan upprätta snabba säkra anslutningar över virtuella nätverk till lokala företags resurser.

Användning av ASE för den här arkitekturen tillåts för följande kontroller och konfigurationer:

- Värd i en skyddad Azure-Virtual Network och nätverks säkerhets regler
- ASE konfigurerad med självsignerat ILB certifikat för HTTPS-kommunikation
- [Läge för intern belastnings utjämning](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Inaktivera [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Ändra [TLS-chiffer](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrol lera [inkommande trafiks N/W-portar](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Brand vägg för webbaserade program – begränsa data](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Tillåt [Azure SQL Database trafik](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

Avsnittet [vägledning och rekommendationer](#guidance-and-recommendations) innehåller ytterligare information om ASE.

**Azure-webbapp**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) gör det möjligt för kunder att bygga och vara värd för webb program i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper (NSG: er)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande NSG: er finns:
- 1 NSG för Application Gateway
- 1 NSG för App Service-miljön
- 1 NSG för Azure SQL Database

Varje NSG: er har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
  - Azure Monitor loggar är anslutna till [NSG-diagnostiken](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerat med motsvarande NSG.

**Azure DNS**: Domain Name System eller DNS ansvarar för översättning (eller matchning) av en webbplats eller ett tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för DNS-domäner som ger namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör det möjligt för kunderna att skala sina program och skapa hög tillgänglighet för tjänster. Load Balancer stöder inkommande och utgående scenarier och ger låg latens, högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner som Azure Storage via Azure Portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Autentisering och auktorisering i AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   Azure SQL Database har kon figurer ATS för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) gör det möjligt för användare att definiera principer för att begränsa åtkomsten till data för att avbryta bearbetningen.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter att referens arkitekturen har distribuerats. Kunderna måste justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för identitets hantering i Azure-miljön:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i AAD, inklusive användare som har åtkomst till Azure SQL Database.
-   Autentisering till programmet utförs med AAD. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure Active Directory för att autentisera programmet att Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) möjliggör exakt fokuserad åtkomst hantering för Azure. Åtkomst till prenumerationen är begränsad till prenumerations administratören och åtkomsten till resurser kan begränsas baserat på användar rollen.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information.  Administratörer kan använda AAD-Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering** Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda data och åtkomst till sådana data:
- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade HSM: er (Hardware Security modules). Nyckel typen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med rollbaserad åtkomst kontroll.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Application Gateway** Arkitekturen minskar risken för säkerhets problem med en Application Gateway med brand vägg för webbaserade program och OWASP-ruleset aktive rad. Fler funktioner är:
- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](../../application-gateway/create-ssl-portal.md)
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brand vägg för webbaserade program](../../application-gateway/waf-overview.md)
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0 ruleset
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälso avsökningar](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.

### <a name="logging-and-auditing"></a>Loggning och granskning
Azure Monitor tillhandahåller omfattande loggning av system-och användar aktiviteter, samt system hälsan. Den samlar in och analyserar data som genererats av resurser i Azure och lokala miljöer.
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar.
- **Logg arkivering**: Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning. Dessa loggar ansluter till Azure Monitor loggar för bearbetning, lagring och instrument panels rapportering.

Dessutom ingår följande övervaknings lösningar som en del av den här arkitekturen:
-   [Active Directory-utvärdering](../../azure-monitor/insights/ad-assessment.md): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Utvärdering av program mot skadlig kod](../../security-center/security-center-install-endpoint-protection.md): Lösningen mot skadlig kod rapporterar om skadlig kod, hot och skydds status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation lösning lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från Application Insights och Azure SQL Database.
-   [Säkerhet och granskning](../../security-center/security-center-intro.md): Säkerhet och granskning-instrumentpanelen ger bättre inblick i säkerhets läget för resurser genom att tillhandahålla mått på säkerhets domäner, viktiga problem, identifieringar, Hot information och vanliga säkerhets frågor.
-   [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Uppdateringshantering](../../automation/automation-update-management.md): Med den Uppdateringshantering lösningen kan kund hantering av säkerhets uppdateringar för operativ systemet, inklusive status för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Azure-aktivitets loggar](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](../../automation/change-tracking.md): Med Ändringsspårning-lösningen kan kunder enkelt identifiera ändringar i miljön.

AzureMonitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i kunders Azure-resurser.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/fedrampPaaSWebAppDFD) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![PaaS webb program för FedRAMP hot modell](images/fedramp-paaswa-threat-model.png?raw=true "PaaS webb program för FedRAMP hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP hög kund ansvar](https://aka.ms/blueprinthighcrm) visas alla säkerhets kontroller som krävs av den höga bas linjen för FedRAMP. Matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP PaaS webapp med hög kontroll](https://aka.ms/fedrampPaaSWebAppCIM) visar alla säkerhets kontroller som krävs av den höga bas linjen FedRAMP. Matrisen innehåller information om vilka kontroller som omfattas av arkitekturen för PaaS-webbprogram, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här PaaS-webbprogrammets referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en VPN-anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
