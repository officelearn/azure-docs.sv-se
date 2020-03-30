---
title: Säker webbapp för PCI DSS | Microsoft-dokument
description: Den här exempelappen implementerar metodtips för säkerhet som förbättrar ditt program och organisationens säkerhetsposition när du utvecklar på Azure.
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
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992619"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Utveckla en säker infrastruktur för en PCI-app

## <a name="overview"></a>Översikt

Den här säkra infrastrukturen för en PCI-app (Payment Card Industry) ger vägledning för distributionen av en Betalningskortsindustriplattform som en tjänstmiljö (PaaS) som är lämplig för insamling, lagring och hämtning av kortinnehavarens data. Den här lösningen automatiserar distribution och konfiguration av Azure-resurser för en gemensam referensarkitektur, vilket visar hur kunderna kan uppfylla specifika säkerhets- och efterlevnadskrav och fungerar som en grund för kunderna att bygga och konfigurera sina egna lösningar på Azure. Lösningen implementerar en delmängd av krav som liknar PCI DSS 3.2 (Payment Card Industry Data Security Standards).

Det här exemplet distribuerar automatiskt en PaaS-referensarkitektur för webbprogram med förkonfigurerade säkerhetskontroller för att hjälpa kunder att uppnå efterlevnad som liknar PCI DSS 3.2-krav. Lösningen består av Azure Resource Manager-mallar och PowerShell-skript som styr resursdistribution och konfiguration.

Du bör följa stegen som beskrivs i den här artikeln sekventiellt för att säkerställa att programkomponenterna är korrekt konfigurerade. Databasen, Azure App Service, Azure Key Vault-instans och Azure Application Gateway-instans beror på varandra.

Distributionsskripten konfigurerar infrastrukturen. När du har kört distributionsskripten måste du göra en viss manuell konfiguration i Azure-portalen för att länka komponenterna och tjänsterna tillsammans.

Det här exemplet riktar sig till erfarna utvecklare på Azure som arbetar inom detaljhandeln och vill skapa en återförsäljarapp med säker Azure-infrastruktur.

> [!NOTE]
> Denna arkitektur är avsedd att fungera som en grund för kunderna att anpassa sig till sina specifika krav och bör inte användas som-är i en produktionsmiljö.

Att distribuera ett program i den här miljön utan ändringar är inte tillräckligt för att helt uppfylla kraven i PCI DSS 3.2. Observera följande:

- Den här arkitekturen ger en baslinje som hjälper kunder att använda Azure på ett PCI DSS 3.2-kompatibelt sätt.
- Kunderna är ansvariga för att genomföra lämplig säkerhets- och efterlevnadsbedömning av alla lösningar som skapats med den här arkitekturen, eftersom kraven kan variera beroende på detaljerna i varje kunds implementering.

När du utvecklar och distribuerar den här appen får du lära dig hur du:

- Skapa en Azure Key Vault-instans och lagra och hämta hemligheter från den.
- Distribuera Azure Database för Azure SQL, konfigurera säkra data och auktorisera åtkomst till den.
- Distribuera Azure-webbappen med App Service-miljö som är en dedikerad isolerad med frontend-brandvägg aEcess.
- Skapa och konfigurera en Azure Application Gateway-instans med en brandvägg som använder [OWASP Top 10 Ruleset](https://coreruleset.org/).
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster.
- Konfigurera Azure-principen och blå utskrifter för att utvärdera efterlevnaden

När du har utvecklat och distribuerat den här appen har du konfigurerat följande exempelwebbapp tillsammans med de konfigurations- och säkerhetsåtgärder som beskrivs.

## <a name="architecture-diagram-and-components"></a>Arkitekturdiagram och komponenter
Appen är ett typiskt n-nivåprogram med tre nivåer. Frontend, baksidan och databasskiktet med integrerade övervaknings- och hemliga hanteringskomponenter visas här:

![App-arkitektur](./media/secure-pci-web-app/architecture.png)

Arkitekturen består av dessa komponenter:

- [App Service Miljö v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Tillhandahåller App Service Environment och belastningsutjämnare för vår programarkitektur.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Tillhandahåller gateway och brandvägg för vår programarkitektur.
- [Programinsikter](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tillhandahåller en utökningsbar APM-tjänst (Application Performance Management) på flera plattformar.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Lagrar och krypterar appens hemligheter och hanterar skapandet av åtkomstpolicyer runt dem.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Tillhandahåller molnbaserad identitets- och åtkomsthanteringstjänst, inloggning och åtkomstresurser.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Tillhandahåller tjänsten som värd för domänen.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Skalar dina program och skapar hög tillgänglighet för dina tjänster
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Tillhandahåller lösning för moderna datalagringsscenarier.
- [Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Tillhandahåller en HTTP-baserad tjänst för att vara värd för webbprogram.
- [Azure-databas för AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Lagrar våra appars data på ett säkert sätt.
- [Azure-ritningar](https://docs.microsoft.com/azure/governance/blueprints/overview/). Ger specifikationer och överensstämmelse med vissa standarder och krav.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure-princip](https://docs.microsoft.com/azure/governance/policy/overview). Utvärderar dina resurser för bristande efterlevnad av tilldelade principer.

## <a name="threat-model"></a>Hotmodell
Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt företag och program och sedan se till att en korrekt begränsningsplan finns på plats.

I det här exemplet användes [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) för att implementera hotmodellering för den säkra exempelappen. Genom att kartlägga komponenterna och dataflödena kan du identifiera problem och hot tidigt i utvecklingsprocessen. Detta sparar tid och pengar senare.

Det här är hotmodellen för exempelappen:

![Hotmodell](./media/secure-pci-web-app/threat-model.png)

Vissa exempelhot och potentiella sårbarheter som verktyget för hotmodellering genererar visas i följande skärmbild. Hotmodellen ger en översikt över den exponerade attackytan och uppmanar utvecklarna att tänka på hur du kan minska problemen.

![Utdata för hotmodell](./media/secure-web-app/threat-model-output.png)

Sql-injektion i föregående hotmodellutdata mildras till exempel genom att sanera användarindata och använda lagrade funktioner i Azure Database för PostgreSQL. Den här begränsningen förhindrar godtycklig körning av frågor under dataläsningar och skrivningar.

Utvecklare förbättrar systemets övergripande säkerhet genom att minska alla hot i hotmodellens utdata.

## <a name="deployment"></a>Distribution
### <a name="prerequisites"></a>Krav
För att få igång programmet måste du installera följande verktyg:

- En kodredigerare för att ändra och visa programkoden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) på din utvecklingsdator.
- [Git](https://git-scm.com/) på ditt system. Git används för att klona källkoden lokalt.
- [jq](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Det här exemplet består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänst för att distribuera resurser inom Azure. Detaljerade distributionsinstruktioner finns [här](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Snabbstart

1.  Klona [this](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) eller hämta GitHub-databasen till din lokala arbetsstation.
2.  Granska 0-Setup-AdministrativeAccountAndPermission.md och kör de angivna kommandona.
3.  Distribuera en testlösning med Contoso-exempeldata eller pilot en första produktionsmiljö.

    Det här skriptet distribuerar Azure-resurser för en demonstration av ett webblager med contoso-exempeldata.

I det här exemplet kör du distributionsskriptet som distribuerar webbappen till App Service och skapar resurserna.

Det finns många sätt att distribuera appar på Azure, bland annat:

- Azure Resource Manager-mallar
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="network"></a>Nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme på 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Nätverkssäkerhetsgrupper(https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor (ACL: er) som tillåter eller nekar trafik inom ett virtuellt nätverk. Nätverkssäkerhetsgrupper kan användas för att skydda trafik på undernät eller enskild VM-nivå. Följande nätverkssäkerhetsgrupper finns:

- 1 nätverkssäkerhetsgrupp för Application Gateway
- 1 nätverkssäkerhetsgrupp för App Service Environment
- 1 nätverkssäkerhetsgrupp för Azure SQL Database
- 1 nätverkssäkerhetsgrupp för skyddsvärd

Var och en av nätverkssäkerhetsgrupperna har specifika portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverkssäkerhetsgrupp:

- Diagnostikloggar ochhttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) händelser( aktiveras och lagras i ett lagringskonto
- Azure Monitor-loggar är anslutna till nätverkssäkerhetsgruppens diagnostik(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG Config
NSG config för App Service Environment bör konfigureras som visas i bilden nedan.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Varje undernät är associerat med motsvarande nätverkssäkerhetsgrupp.

### <a name="config"></a>Konfigurera
Undernät konfigureras enligt bilden nedan.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
DNS (Domain Name System) ansvarar för att översätta (eller lösa) en webbplats eller tjänstnamn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med Azure-infrastruktur. Genom att vara värd för domäner i Azure kan användare hantera DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

### <a name="protect-data"></a>Skydda data
För att skydda data i molnet måste du ta hänsyn till möjliga tillstånd där dina data kan inträffa och vilka kontroller som är tillgängliga för det tillståndet. Metodtips för Azure-datasäkerhet och kryptering gäller följande datatillstånd:

- I vila: Detta inkluderar alla informationslagringsobjekt, behållare och typer som finns statiskt på fysiska medier, oavsett om det är magnetiskt eller optiskt disk.
- Under överföring: När data överförs mellan komponenter, platser eller program är de under överföring. Exempel är överföring över nätverket, över en servicebuss (från lokalt till moln och vice versa, inklusive hybridanslutningar som ExpressRoute) eller under en indata-/utdataprocess.

### <a name="azure-storage"></a>Azure Storage
För att uppfylla kraven på krypterade data i vila använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault för att behålla kontrollen över nycklar som kommer åt och krypterar data. Detta hjälper till att skydda och skydda kortinnehavarens data till stöd för organisatoriska säkerhetsåtaganden och efterlevnadskrav som definieras av PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volymkryptering för datadiskar. Lösningen integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklarna.

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database-instansen använder följande databassäkerhetsåtgärder:

- [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
- [GRANSKNING av SQL-databaser](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) spårar databashändelser och skriver dem till en granskningslogg i ett Azure-lagringskonto.
- Azure SQL Database är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i realtid av databasen, associerade säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering garanterar att lagrade data inte har varit föremål för obehörig åtkomst.
- [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och reagera på potentiella hot när de uppstår genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella sårbarheter, SQL-injektionsattacker och avvikande databasåtkomstmönster.
- [Krypterade kolumner säkerställer](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) att känsliga data aldrig visas som klartext i databassystemet. När du har aktiverat datakryptering kan endast klientprogram eller programservrar med åtkomst till nycklarna komma åt oformaterade data.
- [Dynamisk datamaskering i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig dataexponering genom att maskera data för användare eller program som inte är privilegierade. Dynamisk datamaskering kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta hjälper till att identifiera och minska åtkomsten till data så att den inte avslutar databasen via obehörig åtkomst. Kunderna ansvarar för att justera inställningar för dynamisk datamaskering för att följa sitt databasschema.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till kortinnehavarens data i Azure-miljön:

- Azure Active Directory är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som ansluter till Azure SQL Database.
- Autentisering till programmet utförs med Azure Active Directory. Mer information finns i [Integrating applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) (Integrera program med Azure Active Directory). Dessutom använder krypteringen av databaskolumnen Azure Active Directory för att autentisera programmet till Azure SQL Database. Mer information finns i [hur du skyddar känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Azure-rollbaserad åtkomstkontroll gör det möjligt för administratörer att definiera detaljerade åtkomstbehörigheter för att bevilja endast den mängd åtkomst som användare behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer endast tillåta vissa åtgärder för åtkomst till kortinnehavaredata. Prenumerationsåtkomsten är begränsad till prenumerationsadministratören.
- Azure Active Directory Privilegierad identitetshantering gör det möjligt för kunder att minimera antalet användare som har åtkomst till viss information, till exempel kortinnehavaredata. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst på begäran, just-in-time när det behövs.
- Azure Active Directory Identity Protection identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta åtgärder som är relaterade till en organisations identiteter incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="secrets-management"></a>Hantering av hemligheter
Lösningen använder Azure Key Vault för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault-funktioner hjälper kunder att skydda och komma åt sådana data:

- Principer för avancerad åtkomst konfigureras på behovsbasis.
- Åtkomstprinciper för Key Vault definieras med minsta möjliga behörighet till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har utgångsdatum.
- Alla nycklar i Key Vault skyddas av specialiserade maskinvarusäkerhetsmoduler. Nyckeltypen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Med Key Vault kan du kryptera nycklar och hemligheter (t.ex. autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM)
- Använd RBAC för att tilldela behörigheter till användare, grupper och program i ett visst omfång.
- Använd Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse.
- Diagnostikloggar för Key Vault är aktiverade med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

### <a name="azure-security-center"></a>Azure Security Center
Med Azure Security Center kan kunder centralt tillämpa och hantera säkerhetsprinciper över arbetsbelastningar, begränsa exponeringen för hot och identifiera och svara på attacker. Dessutom får Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations- och tjänstrekommendationer för att förbättra säkerhetspositionen och skydda data.

Azure Security Center använder en mängd olika identifieringsfunktioner för att varna kunder om potentiella attacker som är inriktade på deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning fördefinierade säkerhetsaviseringar som utlöses när ett hot eller misstänkt aktivitet äger rum. Anpassade varningsregler i Azure Security Center gör det möjligt för kunder att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center tillhandahåller prioriterade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En rapport om hotinformation genereras för varje upptäckt hot för att hjälpa incidenthanteringsgrupper att utreda och åtgärda hot.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Arkitekturen minskar risken för säkerhetsproblem med hjälp av en Azure Application Gateway med en brandvägg för webbprogram konfigurerad och OWASP-regeluppsättningen aktiverad. Ytterligare funktioner inkluderar:

- End-to-end-SSL
- Inaktivera TLS v1.0 och v1.1
- Aktivera TLSv1.2
- Brandvägg för webbprogram (förebyggande läge)
- Förebyggande läge med OWASP 3.0-regeluppsättning
- Aktivera diagnostikloggning
- Anpassade hälsoavsökningar
- Azure Security Center och Azure Advisor ger ytterligare skydd och meddelanden. Azure Security Center tillhandahåller också ett ryktessystem.

### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster loggar i stor utsträckning system- och användaraktivitet samt systemhälsa:

- [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger insikt i åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa till att fastställa en operationsinitiator, förekomsttid och status.
- [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) innehåller alla loggar som avges av varje resurs. Dessa loggar inkluderar Windows händelsesystemloggar, Azure Storage-loggar, Key Vault granskningsloggar och Application Gateway åtkomst och brandväggsloggar. Alla diagnostikloggar skriv till ett centraliserat och krypterat Azure-lagringskonto för arkivering. Kvarhållningen är användarkonfigurerbar, upp till 730 dagar, för att uppfylla organisationsspecifika lagringskrav.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Dessa loggar konsolideras i [Azure Monitor-loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrumentpanelsrapportering. När data har samlats in är de ordnade i separata tabeller för varje datatyp i Log Analytics-arbetsytor, vilket gör att alla data kan analyseras tillsammans oavsett dess ursprungliga källa. Dessutom integreras Azure Security Center med Azure Monitor-loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhetshändelsedata och kombinera dem med data från andra tjänster.

Följande [Azure-övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check-lösningen utvärderar risken och hälsan för servermiljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL Health Check-lösningen utvärderar risken och hälsan för servermiljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent health-lösningen rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
- [Aktivitetslogganalys:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)Lösningen för Aktivitetslogganalys hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i sina Azure-resurser.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar Application Performance Management-tjänst för webbutvecklare på flera plattformar. Application Insights identifierar prestandaavvikelser och kunder kan använda den för att övervaka live-webbprogrammet. Den innehåller kraftfulla analysverktyg som hjälper kunderna att diagnostisera problem och förstå vad användarna faktiskt gör med sin app. Den är utformad för att hjälpa kunderna att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-key-vault"></a>Azure Key Vault
Skapa ett valv för den organisation där nycklar ska lagras och underhåll ansvarsskyldighet för operativa uppgifter som nedan:

- Data som lagras i Key Vault innehåller:

   - Intraktningsnyckel för program
   - Åtkomstnyckel för datalagring
   - Anslutningssträng
   - Namn på datatabell
   - Användarreferenser

- Principer för avancerad åtkomst konfigureras på behovsbasis
- Åtkomstprinciper för Key Vault definieras med minsta möjliga behörighet till nycklar och hemligheter
- Alla nycklar och hemligheter i Key Vault har utgångsdatum
- Alla tangenter i Key Vault skyddas av HSM [Nyckeltyp = HSM-skyddad 2048-bitars RSA-nyckel]
- Alla användare/identiteter beviljas lägsta obligatoriska behörigheter med hjälp av rollbaserad åtkomstkontroll (RBAC)
- Program delar inte ett Key Vault om de inte litar på varandra och de behöver tillgång till samma hemligheter vid körning
- Diagnostikloggar för Key Vault är aktiverade med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att korrekt upprätta en anslutning till de resurser som distribueras som en del av den här PaaS-referensarkitekturen för webbprogram. Genom att konfigurera en VPN eller ExpressRoute på lämpligt sätt kan kunderna lägga till ett skyddslager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan en virtuell privat anslutning mellan ett lokalt nätverk och ett Virtuellt Azure-nätverk skapas. Den här anslutningen sker via Internet och gör det möjligt för kunder att säkert "tunnel" information i en krypterad länk mellan kundens nätverk och Azure. Site-to-Site VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar i årtionden. IPsec-tunnelläget används i det här alternativet som en krypteringsmekanism.

Eftersom trafiken i VPN-tunneln korsar Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat, ännu säkrare anslutningsalternativ. Azure ExpressRoute är en dedikerad WAN-länk mellan Azure och en lokal plats eller en Exchange-värdleverantör. Eftersom ExpressRoute-anslutningar inte går via Internet erbjuder dessa anslutningar mer tillförlitlighet, snabbare hastigheter, lägre fördröjningar och högre säkerhet än typiska anslutningar via Internet. Eftersom detta är en direkt anslutning till kundens telekommunikationsleverantör, färdas uppgifterna inte över Internet och är därför inte exponerade för dem.

Metodtips för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure är [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Kostnadsöverväganden
Om du inte redan har ett Azure-konto kan du skapa ett kostnadsfritt konto. Gå till den [kostnadsfria kontosidan](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnadsfritt Azure-konto och lär dig vilka produkter som är lediga i 12 månader.

Om du vill distribuera resurserna i exempelappen med säkerhetsfunktionerna måste du betala för vissa premiumfunktioner. När appen skalas och de kostnadsfria nivåer och utvärderingsversioner som erbjuds av Azure måste uppgraderas för att uppfylla programmets krav kan dina kostnader öka. Använd [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta dina kostnader.

## <a name="next-steps"></a>Nästa steg
Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
