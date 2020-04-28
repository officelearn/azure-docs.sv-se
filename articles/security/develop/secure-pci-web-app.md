---
title: Säker webbapp för PCI DSS | Microsoft Docs
description: Den här exempel appen implementerar säkerhets metod tips som förbättrar ditt program och din organisations säkerhets position när du utvecklar på Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187587"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Utveckla en säker infrastruktur för en PCI-app

## <a name="overview"></a>Översikt

Den här säkra infrastrukturen för en-PCI-app (Payment Card Industry) ger vägledning för distribution av en PaaS-miljö (Platform as a Service) som passar för insamling, lagring och hämtning av kort korts data. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en gemensam referens arkitektur och demonstrerar hur kunder kan uppfylla specifika krav på säkerhet och efterlevnad och fungerar som en grund för kunderna att bygga och konfigurera egna lösningar på Azure. Lösningen implementerar en delmängd av krav som liknar betalnings korts bransch data säkerhets standarder (PCI DSS 3,2).

Det här exemplet distribuerar automatiskt en PaaS-webbprograms referens arkitektur med förkonfigurerade säkerhets kontroller för att hjälpa kunderna att uppnå kompatibilitet som liknar PCI DSS 3,2-krav. Lösningen består av Azure Resource Manager mallar och PowerShell-skript som hjälper till att distribuera och konfigurera resurser.

Följ stegen som beskrivs i den här artikeln för att se till att program komponenterna är korrekt konfigurerade. Databasen, Azure App Service, Azure Key Vault instansen och Azure Application Gateway-instansen är beroende av varandra.

Distributions skripten konfigurerar infrastrukturen. När du har kört distributions skripten måste du göra en manuell konfiguration i Azure Portal för att länka komponenterna och tjänsterna tillsammans.

Det här exemplet är riktat mot erfarna utvecklare på Azure som arbetar inom detalj handels branschen och vill bygga en butiks app med säker Azure-infrastruktur.

> [!NOTE]
> Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.

Det räcker inte att distribuera ett program i den här miljön utan modifiering för att uppfylla kraven i PCI DSS 3,2. Observera följande:

- Den här arkitekturen tillhandahåller en bas linje som hjälper kunder att använda Azure på ett PCI DSS 3,2-kompatibelt sätt.
- Kunderna ansvarar för att utföra lämpliga bedömningar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

När du utvecklar och distribuerar den här appen får du lära dig att:

- Skapa en Azure Key Vault-instans och lagra och hämta hemligheter från den.
- Distribuera Azure Database för Azure SQL, konfigurera säkra data och ge åtkomst till den.
- Distribuera Azure-webbappen med App Service miljö som är en dedikerad isolerad brand Väggs åtkomst på klient sidan.
- Skapa och konfigurera en Azure Application Gateway-instans med en brand vägg som använder [OWASP 10 högsta ruleset](https://coreruleset.org/).
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster.
- Konfigurera Azure Policy och Azure-ritningar för att utvärdera kompatibiliteten.

När du har utvecklat och distribuerat den här appen har du ställt in följande exempel-webbapp tillsammans med de konfigurations-och säkerhets åtgärder som beskrivs.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Appen är ett typiskt n-Nivåprogram med tre nivåer. Klient delen, Server delen och databas lagret med integrerade komponenter för övervakning och hemlig hantering visas här:

![App-arkitektur](./media/secure-pci-web-app/architecture.png)

Arkitekturen består av följande komponenter:

- [App Service-miljön v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Tillhandahåller App Service-miljön och belastningsutjämnare för vår program arkitektur.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Tillhandahåller gateway och brand vägg för vår program arkitektur.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tillhandahåller en utöknings bar APM-tjänst (Application Performance Management) på flera plattformar.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Lagrar och krypterar vår Apps hemligheter och hanterar skapandet av åtkomst principer runt dem.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Tillhandahåller molnbaserad identitets-och åtkomst hanterings tjänst, logga in och få åtkomst till resurser.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Tillhandahåller tjänsten som värd för domänen.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Skalar dina program och skapar hög tillgänglighet för dina tjänster
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Tillhandahåller lösning för moderna data lagrings scenarier.
- [Azure-webbapp](https://docs.microsoft.com/azure/app-service/overview/).  Tillhandahåller en HTTP-baserad tjänst för att vara värd för webb program.
- [Azure Database för AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Lagra appens data på ett säkert sätt.
- [Azure-ritningar](https://docs.microsoft.com/azure/governance/blueprints/overview/). Innehåller specifikationer och efterlevnad av vissa standarder och krav.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview). Utvärderar resurser för inkompatibilitet med tilldelade principer.

## <a name="threat-model"></a>Hot modell
Hot modellering är en process där du kan identifiera potentiella säkerhetshot för ditt företag och program och sedan se till att en lämplig minsknings plan är på plats.

Det här exemplet använde [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) för att implementera hot modellering för appen säker exempel. Genom att skriva diagram över komponenterna och data flödena kan du identifiera problem och hot tidigt i utvecklings processen. Detta sparar tid och pengar senare.

Det här är Hot modellen för exempel appen:

![Hot modell](./media/secure-pci-web-app/threat-model.png)

Några exempel på hot och eventuella sårbarheter som verktyget för Threat Modeling genererar visas på följande skärm bild. Hot modellen ger en översikt över angrepps ytan som exponeras och gör det möjligt för utvecklarna att tänka på hur de kan åtgärda problemen.

![Hot modellens utdata](./media/secure-web-app/threat-model-output.png)

SQL-inmatning i föregående hot modells utdata minimeras till exempel genom att de sanerade indata matas in och genom att använda lagrade funktioner i Azure Database for PostgreSQL. Den här lösningen förhindrar körning av frågor vid data läsningar och skrivningar.

Utvecklare förbättrar den övergripande säkerheten för systemet genom att minimera varje hot i Hot modellens utdata.

## <a name="deployment"></a>Distribution
### <a name="prerequisites"></a>Krav
För att komma igång med programmet måste du installera följande verktyg:

- En kod redigerare för att ändra och Visa program koden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) på din utvecklings dator.
- [Git](https://git-scm.com/) i systemet. Git används för att klona käll koden lokalt.
- [JQ](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Det här exemplet består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Managers API-tjänst för att distribuera resurser i Azure. Detaljerade distributions anvisningar finns [här](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Snabbstart

1.  Klona eller ladda ned [den här](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub-lagringsplatsen till din lokala arbets Station.
2.  Granska 0-Setup-AdministrativeAccountAndPermission.md och kör de tillhandahållna kommandona.
3.  Distribuera en test lösning med contoso-exempel data eller pilot en inledande produktions miljö.

    Det här skriptet distribuerar Azure-resurser för en demonstration av ett webb Arkiv med exempel data från contoso.

I det här exemplet kör du distributions skriptet som distribuerar webbappen till App Service och skapar resurserna.

Det finns många sätt att distribuera appar på Azure, inklusive:

- Azure Resource Manager-mallar
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="network"></a>Nätverk
Arkitekturen definierar ett privat Virtual Network med ett adress utrymme på 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Nätverks säkerhets grupper (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller Access Control listor (ACL: er) som tillåter eller nekar trafik i ett virtuellt nätverk. Nätverks säkerhets grupper kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande nätverks säkerhets grupper finns:

- 1 nätverks säkerhets grupp för Application Gateway
- 1 nätverks säkerhets grupp för App Service-miljön
- 1 nätverks säkerhets grupp för Azure SQL Database
- 1 nätverks säkerhets grupp för skydds-värd

Varje nätverks säkerhets grupp har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverks säkerhets grupp:

- Diagnostikloggar och händelser (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagrade i ett lagrings konto
- Azure Monitor loggar är anslutna till nätverks säkerhets gruppens diagnostik (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG-konfiguration
NSG-konfiguration för App Service-miljön ska konfigureras så som visas på bilden nedan.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Varje undernät är associerat med motsvarande nätverks säkerhets grupp.

### <a name="config"></a>Konfigurera
Undernät konfigureras på det sätt som visas i bilden nedan.
 ![Konfigurationsfil](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Domain Name System (DNS) ansvarar för översättning (eller matchning av) en webbplats eller ett tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för DNS-domäner som ger namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

### <a name="protect-data"></a>Skydda data
För att skydda data i molnet måste du ta hänsyn till de tillstånd som dina data kan utföra och vilka kontroller som är tillgängliga för det tillståndet. Metod tips för Azure Data Security och kryptering är relaterade till följande data tillstånd:

- I vila: Detta omfattar alla informations lagrings objekt, behållare och typer som finns statiskt på fysiska media, oavsett om det gäller magnet eller optisk disk.
- Under överföring: när data överförs mellan komponenter, platser eller program, är den under överföring. Exempel överförs över nätverket, via en Service Bus (från lokalt till molnet och vice versa, inklusive hybrid anslutningar som ExpressRoute) eller under en indata/utdata-process.

### <a name="azure-storage"></a>Azure Storage
För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault för att behålla kontrollen över nycklar som kommer åt och krypterar data. Detta hjälper till att skydda och skydda kort korts data i stöd för organisatoriska säkerhets åtaganden och krav på efterlevnad som definieras av PCI DSS 3,2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:

- [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
- [SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
- Azure SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte omfattas av obehörig åtkomst.
- [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- Med [SQL hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
- [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till icke-privilegierade användare eller program. Dynamisk data maskning kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta hjälper till att identifiera och minska åtkomsten till data så att den inte avslutar databasen via obehörig åtkomst. Kunderna ansvarar för att justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för att hantera åtkomst till kort innehavares data i Azure-miljön:

- Azure Active Directory är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som har åtkomst till Azure SQL Database.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) (Integrera program med Azure Active Directory). Dessutom använder databasens kolumn kryptering Azure Active Directory för att autentisera programmet att Azure SQL Database. Mer information finns i [så här skyddar du känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Med rollbaserad åtkomst kontroll i Azure kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data för kort innehavare. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- Azure Active Directory Privileged Identity Management gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information, till exempel kort korts data. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- Azure Active Directory Identity Protection identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som är relaterade till en organisations identiteter, och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="secrets-management"></a>Hemligheter, hantering
Lösningen använder Azure Key Vault för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda och komma åt sådana data:

- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar). PFX-filer och lösen ord) med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules)
- Använd RBAC för att tilldela behörigheter till användare, grupper och program i ett visst omfång.
- Använd Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

### <a name="azure-security-center"></a>Azure Security Center
Med Azure Security Center kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning fördefinierade säkerhets aviseringar som utlöses när ett hot eller misstänkt aktivitet äger rum. Anpassade aviserings regler i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En hot informations rapport genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Arkitekturen minskar risken för säkerhets problem med en Azure Application Gateway med en brand vägg för webbaserade program konfigurerad, och OWASP-ruleset har Aktiver ATS. Fler funktioner är:

- Slut punkt till slut punkt – SSL
- Inaktivera TLS v 1.0 och v 1.1
- Aktivera TLSv 1.2
- Brand vägg för webbaserade program (skydds läge)
- Skydds läge med OWASP 3,0 ruleset
- Aktivera diagnostikloggning
- Anpassade hälso avsökningar
- Azure Security Center och Azure Advisor ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.

### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:

- [Aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp inom Log Analytics arbets ytor, vilket innebär att alla data kan analyseras tillsammans oavsett den ursprungliga källan. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:

- [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): den Active Directory hälso kontroll lösningen utvärderar hälso-och hälso tillståndet i Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [SQL-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): med SQL-hälsokontroll-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
- [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Aktivitetslogganalys lösningen hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utöknings bar hanterings tjänst för program prestanda för webbutvecklare på flera plattformar. Application Insights identifierar prestanda avvikelser och kunder kan använda den för att övervaka Live-webbprogrammet. Den innehåller kraftfulla analys verktyg som hjälper kunder att diagnostisera problem och förstå vad användare faktiskt gör med sina appar. Det är utformat för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-key-vault"></a>Azure Key Vault
Skapa ett valv för organisationen där du kan lagra nycklar och upprätthålla ansvaret för drift uppgifter som nedan:

- Data som lagras i Key Vault innehåller:

   - Insikts nyckel för program
   - Åtkomst nyckel för data lagring
   - Anslutningssträng
   - Data tabell namn
   - Användarautentiseringsuppgifter

- Avancerade åtkomst principer konfigureras på grund av behov
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter
- Alla nycklar och hemligheter i Key Vault har förfallo datum
- Alla nycklar i Key Vault skyddas av HSM [Key Type = HSM Protected 2048-bitars RSA Key]
- Alla användare/identiteter beviljas minst de behörigheter som krävs med hjälp av rollbaserad Access Control (RBAC)
- Program delar inte en Key Vault om de inte litar på varandra och de behöver åtkomst till samma hemligheter vid körning
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här PaaS-webbprogrammets referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. IPsec-tunnelläge används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Kostnadsöverväganden
Om du inte redan har ett Azure-konto kan du skapa ett kostnads fritt. Gå till [sidan kostnads fritt konto](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnads fritt Azure-konto och lär dig vilka produkter som är kostnads fria i 12 månader.

Om du vill distribuera resurserna i exempel appen med säkerhetsfunktionerna måste du betala för vissa Premium-funktioner. När appen skalas och de kostnads fria nivåerna och utvärderingarna som erbjuds av Azure måste uppgraderas för att uppfylla program kraven kan kostnaderna öka. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för Azure för att beräkna dina kostnader.

## <a name="next-steps"></a>Nästa steg
Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
