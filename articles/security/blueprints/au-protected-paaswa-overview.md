---
title: PaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure
description: PaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: d1857d0cb1b45be5b6ce4e1dd34e8398786f54fb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946909"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>PaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure

## <a name="overview"></a>Översikt

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för distribution av en PaaS-miljö (Platform as a Service) som lämpar sig för insamling, lagring och hämtning av AU-skyddade myndighets data som är kompatibla med målen för Den australiska regeringens informations hand bok (ISM) som produceras av australiskt Signals direktorat (ASD). Den här skissen visar en gemensam referens arkitektur och hjälper till att visa korrekt hantering av känsliga myndighets data i en säker, kompatibel miljö med flera nivåer.

Denna referens arkitektur, implementerings guide och hot modell tillhandahåller en grund för kunderna att utföra egna planerings-och system ackrediterings processer, vilket hjälper kunderna att distribuera arbets belastningar till Azure på ett ASD-kompatibelt sätt. Kunder kan välja att implementera en Azure-VPN Gateway eller ExpressRoute för att använda federerade tjänster och integrera lokala resurser med Azure-resurser. Kunderna måste fundera på säkerhets konsekvenserna av att använda lokala resurser. Ytterligare konfiguration krävs för att uppfylla alla krav, eftersom de kan variera beroende på vad som gäller för varje kunds implementering.

För att kunna använda ASD-kompatibilitet krävs att en information Security-registrerad bedömaren granskar systemet. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här lösningen ger en referens arkitektur för ett PaaS-webbprogram med en Azure SQL Database Server del. Webb programmet finns i ett isolerat Azure App Service-miljön, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Miljö belastningen balanserar trafiken för webb programmet på virtuella datorer som hanteras av Azure. Alla webb program anslutningar kräver TLS med en lägsta version av 1,2. Den här arkitekturen omfattar även nätverks säkerhets grupper, en Application Gateway, Azure DNS och Load Balancer.

Arkitekturen kan leverera en säker hybrid miljö som utökar ett lokalt nätverk till Azure, vilket gör att webbaserade arbets belastningar kan nås på ett säkert sätt av företags användare i en organisations privata lokala nätverk eller från Internet. För lokala lösningar är kunden både konto ansvarig och ansvarig för alla aspekter av säkerhet, åtgärder och efterlevnad.

De Azure-resurser som ingår i den här lösningen kan ansluta till en lokal nätverks-eller datacenter-plats (t. ex. CDC i Canberra) via en IPSec-VPN med hjälp av en VPN Gateway och via ExpressRoute. Beslut om att använda en VPN-anslutning bör göras med klassificeringen av överförda data och nätverks Sök vägen i åtanke. Kunder som kör storskaliga och verksamhets kritiska arbets belastningar med Big data-krav bör överväga en hybrid nätverks arkitektur med ExpressRoute för privat nätverks anslutning till Azure-tjänster. Mer information om anslutnings metoder till Azure finns i avsnittet [vägledning och rekommendationer](#guidance-and-recommendations) .

Federation med Azure Active Directory ska användas för att göra det möjligt för användare att autentisera med lokala autentiseringsuppgifter och få åtkomst till alla resurser i molnet med hjälp av en lokal Active Directory Federation Services (AD FS)-infrastruktur. Active Directory Federation Services (AD FS) kan tillhandahålla enkla, säkra identitets Federations-och webb funktioner för enkel inloggning för den här hybrid miljön. Mer information Azure Active Directory-installationen finns i avsnittet [vägledning och rekommendationer](#guidance-and-recommendations) .

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda region för återhämtning. Azure-regioner distribueras i elastiska region par och geografiskt redundant lagring garanterar att data replikeras till den andra regionen med tre kopior. Detta förhindrar en negativ händelse på kundens primära data plats, vilket leder till förlust av data.

För ökad säkerhet hanteras alla Azure-resurser i den här lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory rollbaserad åtkomst kontroll används för att kontrol lera åtkomsten till distribuerade resurser och nycklar i Azure Key Vault. System hälsan övervakas via Azure Security Center och Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel. Azure Application Gateway konfigureras som en brand vägg i skydds läge och tillåter inte trafik som inte är TLS v 1.2 eller senare. Lösningen använder Azure Application Service Environment v2 för att isolera webb nivån i en miljö som inte har flera innehavare.

![PaaS webb program för au-skyddad referens arkitektur](images/au-protected-paaswa-architecture.png?raw=true "PaaS webb program för au-skyddat referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Application Gateway
    - Brandvägg för webbappar
        - Brand Väggs läge: skydd
        - Regel uppsättning: OWASP
        - Lyssnar port: 443
- Application Insights
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Monitor-loggar
- Azure Virtual Network
    - (1)/16 nätverk
    - (4)/24 nätverk
    - Nätverkssäkerhetsgrupper
- Nätverkssäkerhetsgrupper
- Recovery Services valv
- Azure-webbapp

Den här skissen innehåller Azure-tjänster som inte har certifierats för användning på den skyddade klassificeringen av australisk cyberhot Security Centre (ACSC). Microsoft rekommenderar att kunderna granskar publicerade säkerhets-och gransknings rapporter som är relaterade till dessa Azure-tjänster och använder sina riskhanterings ramverk för att avgöra om Azure-tjänsten är lämplig för intern ackreditering och användning på Skyddad klassificering.

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) gör det möjligt för kunderna att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, koordinerad åtgärd. Kunder använder en mall för distribution och mallen kan fungera för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings-och taggnings funktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger användare åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärran sluten trafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure-diagnostik tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av virtuella dator resurser när den inte används

**App Service-miljön v2**: [Azure App Service-miljön](https://docs.microsoft.com/azure/app-service/environment/intro) är en app service-funktion som ger en helt isolerad och dedikerad miljö för säker körning av App Service program i stor skala.

App Service miljöer är isolerade för att bara köra en enskild kunds program och distribueras alltid till ett virtuellt nätverk. Kunderna har detaljerad kontroll över både inkommande och utgående program nätverks trafik, och program kan upprätta snabba säkra anslutningar över virtuella nätverk till lokala företags resurser.

Användning av App Service miljöer för den här arkitekturen tillåter följande kontroller och konfigurationer:

- App Service miljöer måste konfigureras för att använda den isolerade tjänst planen
    - Konfigurera olika App Service miljöer för program med olika klassificeringar
- Värd i ett skyddat Azure Virtual Network-och nätverks säkerhets regler
- App Service miljöer som kon figurer ATS med ett självsignerat internt belastnings Utjämnings certifikat för HTTPS-kommunikation. Som bästa praxis rekommenderar Microsoft att du använder en betrodd certifikat utfärdare för förbättrad säkerhet.
- [Läge för intern belastnings utjämning](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (läge 3)
- Inaktivera [TLS v 1.0 och v 1.1](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Ändra [TLS-chiffer](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Kontrol lera [inkommande trafiks N/W-portar](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Brand vägg för webbaserade program – begränsa data](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Tillåt [Azure SQL Database trafik](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Azure-webbapp**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) gör det möjligt för kunder att bygga och vara värd för webb program i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps Services eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. Nätverks säkerhets grupper kan användas för att skydda trafik i ett undernät eller på en enskild virtuell dator nivå. Följande nätverks säkerhets grupper finns:
- 1 nätverks säkerhets grupp för Application Gateway
- 1 nätverks säkerhets grupp för App Service-miljön
- 1 nätverks säkerhets grupp för Azure SQL Database
- 1 nätverks säkerhets grupp för skydds-värd

Varje nätverks säkerhets grupp har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverks säkerhets grupp:

  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
  - Azure Monitor loggar är anslutna till [nätverks säkerhets gruppens diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerat med motsvarande nätverks säkerhets grupp.

**Azure DNS**: Domain Name System eller DNS ansvarar för översättning (eller matchning) av en webbplats eller ett tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för DNS-domäner som ger namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör det möjligt för kunderna att skala sina program och skapa hög tillgänglighet för tjänster. Load Balancer stöder inkommande och utgående scenarier och ger låg latens, högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. 

För skyddade data som överförs från kundägda nätverk använder arkitekturen Azure Internet eller ExpressRoute med en VPN Gateway som kon figurer ATS med IPSEC.

Dessutom sker alla transaktioner till Azure via hanterings portalen för Azure via HTTPS med TLS v 1.2.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Detta hjälper till att skydda och skydda data som stöd för organisationens säkerhets åtaganden och krav på efterlevnad som definieras av den australiska regeringens ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   Azure SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte omfattas av obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster. SQL hot identifiering integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och Rekommenderad åtgärd för att undersöka och minimera hotet.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till icke-privilegierade användare eller program. Dynamisk data maskning kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta bidrar till att minska åtkomsten så att känsliga data inte avslutar databasen via obehörig åtkomst. Kunderna måste justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="identity-management"></a>Identitetshantering
Kunder kan använda lokala Active Directory federerade tjänster för att federera med [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), som är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integrera lokala kataloger med Azure Active Directory. Alla användare i den här lösningen kräver Azure Active Directory konton, inklusive användare som har åtkomst till Azure SQL Database. Med Federations inloggning kan användarna logga in till Azure Active Directory och autentisera till Azure-resurser med lokala autentiseringsuppgifter.

Dessutom kan följande Azure Active Directory funktioner hantera åtkomst till data i Azure-miljön:
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure Active Directory för att autentisera programmet att Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Med [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar organisationens identiteter, konfigurerar automatiska svar på identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

**Azure Multi-Factor Authentication**: För att skydda identiteter bör Multi-Factor Authentication implementeras. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) är en lättanvänd, skalbar och tillförlitlig lösning som ger en andra autentiseringsmetod för att skydda användare. Azure Multi-Factor Authentication använder kraften i molnet och integreras med lokala Active Directory och anpassade program. Det här skyddet har utökats till verksamhets kritiska scenarier med hög volym.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda data:
- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en säkerhetsmodul för maskin varu säkerhet som skyddas 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med rollbaserad åtkomst kontroll.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

**Application Gateway**: Arkitekturen minskar risken för säkerhets problem med en Application Gateway med en brand vägg för webbaserade program och OWASP-ruleset aktive rad. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL](../../application-gateway/create-ssl-portal.md) -avlastning
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brand vägg för webbaserade program](../../application-gateway/waf-overview.md) (skydds läge)
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0 ruleset
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälso avsökningar](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp, som låter alla data analyseras tillsammans oavsett originalkälla. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](../../azure-monitor/insights/ad-assessment.md): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från Azure SQL Database. Med Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen kan kunder enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.

Azure-Network Watcher: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) innehåller verktyg för att övervaka, diagnostisera, Visa mått och aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk.  Samväldet entiteter bör implementera Network Watcher flödes loggar för NSG: er och Virtual Machines. Dessa loggar bör lagras i ett dedikerat lagrings konto som bara säkerhets loggar lagras i och åtkomst till lagrings kontot bör skyddas med rollbaserad åtkomst kontroll.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/au-protected-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![PaaS webb program för au-skyddad hot modell](images/au-protected-paaswa-threat-model.png?raw=true "PaaS-webbprogram för au-skyddat hot modell diagram")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den här dokumentationen för efterlevnad skapas av Microsoft baserat på plattformar och tjänster som är tillgängliga från Microsoft. På grund av de många olika kund distributioner ger den här dokumentationen en generaliserad metod för en lösning som bara finns i Azure-miljön. Kunder kan identifiera och använda alternativa produkter och tjänster baserat på egna drift miljöer och affärs resultat. Kunder som väljer att använda lokala resurser måste hantera säkerheten och åtgärderna för de lokala resurserna. Den dokumenterade lösningen kan anpassas av kunder för att lösa sina särskilda lokala och säkerhets krav.

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-au-skyddad kund ansvar](https://aka.ms/au-protected-crm) visas alla säkerhets kontroller som krävs av au-prot. Den här matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

Den [handlingsplan för säkerhet och efterlevnad i Azure-au-skyddade PaaS webb program implementerings mat ris](https://aka.ms/au-protected-paaswa-cim) innehåller information om vilka au-skyddade kontroller som hanteras av PaaS-webbappens arkitektur, inklusive detaljerade beskrivningar av Hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute

För sekretessbelagd information måste en säker IPSec-tunnel konfigureras för att upprätta en anslutning till resurserna som distribueras som en del av den här IaaS-webbprogramets referens arkitektur. Genom att konfigurera en IPSec-VPN på ett korrekt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker IPSec VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk. Den här anslutningen kan ske via Internet och gör det möjligt för kunder att på ett säkert sätt ange "tunnel" information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. 

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft ett privat anslutnings alternativ. Azure dataExpressRoutes är en dedikerad länk mellan Azure och en lokal plats eller en Exchange-värd leverantör och betraktas som ett privat nätverk. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet och lägre fördröjning än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

För att skydda klassificerade data, oavsett om de använder Internet eller Azure-ExpressRoute, måste kunderna konfigurera IPSec VPN genom att använda följande inställningar:

• Kundens VPN-initierare måste konfigureras för en SA-livstid som inte är längre än 14400 sekunder.
• Kundens VPN-initierare måste inaktivera IKEv1 aggressivt läge.
• Kundens VPN-initierare måste konfigurera PFS (Perfect Forward Secrecy).
• Kundens VPN-initierare måste konfigurera användningen av HMAC-SHA256 eller större.

Konfigurations alternativ för VPN-enheter och IPSec/IKE-parametrar är [tillgängliga](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) för granskning.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med Azure Active Directory i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md).

Dessutom tillåter [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) kunder att konfigurera Federation med lokala [Active Directory Federation Services (AD FS)]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) och Azure Active Directory. Med Federations inloggning kan kunderna göra det möjligt för användarna att logga in på Azure Active Directory-baserade tjänster med sina lokala lösen ord och utan att behöva ange sina lösen ord på nytt i företags nätverket. Genom att använda Federations alternativet med Active Directory Federation Services (AD FS) kan du distribuera en ny installation av Active Directory Federation Services (AD FS), eller så kan du ange en befintlig installation i en Windows Server 2012 R2-grupp.

För att förhindra att klassificerade data synkroniseras till Azure Active Directory kan kunderna begränsa de attribut som replikeras till Azure Active Directory genom att använda följande inställningar i Azure Active Directory Connect:

- [Aktivera filtrering](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Inaktivera synkronisering av lösen ords-hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
-   [Inaktivera tillbakaskrivning av lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Inaktivera tillbakaskrivning av enhet](../../active-directory/hybrid/how-to-connect-device-writeback.md)
-   Lämna standardinställningarna för [förhindra oavsiktliga borttagningar](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) och [automatisk uppgradering](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)


## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
