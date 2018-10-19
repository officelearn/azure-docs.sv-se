---
title: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för FFIEC
description: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för FFIEC
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: f4c25a65583fe700466cd63e5346316d0149b35a
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409296"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Azure-säkerhet och efterlevnad skiss: PaaS webbprogram för FFIEC ekonomiska tjänster

## <a name="overview"></a>Översikt

Den här Azure-säkerhet och efterlevnad skissen Automation ger vägledning för distribution av en plattform som en tjänst (PaaS)-miljö som är lämplig för insamling, lagring och hämtning av finansiella data regleras av Federal finansinstitut Undersökning rådet (FFIEC). Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att bygga och Konfigurera sina egna lösningar på Azure. Lösningen implementerar en deluppsättning av krav från FFIEC handböcker. Mer information om FFIEC krav och den här lösningen finns i den [efterlevnad dokumentation](#compliance-documentation) avsnittet.

Den här Azure-säkerhet och efterlevnad skissen Automation distribuerar du en PaaS web Referensarkitektur för program med förkonfigurerade säkerhetskontroller för att hjälpa kunder att uppnå efterlevnad med FFIEC krav. Lösningen består av Azure Resource Manager-mallar och PowerShell-skript som hjälper resource distribution och konfiguration.

Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Distribuera ett program i den här miljön utan ändringar är inte tillräckliga för att helt uppfylla kraven för FFIEC mål. Observera följande:
- Den här arkitekturen ger en baslinje för att hjälpa kunder som använder Azure på ett sätt som FFIEC-kompatibel.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömning av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

För att uppnå FFIEC-kompatibilitet krävs att kvalificerade granskare intyga en lösning för produktion-kund. Granskningar är övervakas av granskare från FFIECS medlem instanser, däribland styrelsen för federala reservera System (FRB), de federala insättning Insurance Corporation (FDIC), National Credit Union Administration (NCUA), för Comptroller Valuta (OCC) och konsumenten finansiella Protection Bureau (CFPB). Dessa granskare intyga att granskningar är slutfört av bedömare som hanterar oberoende från granskad institutionen. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

Klicka på [här](https://aka.ms/ffiec-paaswa-repo) anvisningar för distribution.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här Azure-säkerhet och efterlevnad skissen Automation distribuerar du en Referensarkitektur för ett PaaS-webbprogram med en Azure SQL Database-serverdel. Webbprogrammet är värd för en isolerad Azure App Service Environment, vilket är en privat, dedikerad miljö i ett Azure-datacenter. Den miljö belastningsutjämning trafik för webbprogrammet över virtuella datorer som hanteras av Azure. Den här arkitekturen omfattar också nätverkssäkerhetsgrupper, ett Application Gateway, Azure DNS och belastningsutjämnare.

Azure SQL-databaser kan konfigureras med columnstore-index för förbättrad analys och rapportering. Azure SQL-databaser kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda datacenter för återhämtning. Geografiskt redundant lagring garanterar att data ska replikeras till ett sekundärt datacenter hundratals mil bort och lagras igen som tre kopior inom det datacenter som förhindrar att en incident på kundens primära datacenter vilket leder till förlust av data.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.

Azure SQL-databas är ofta hanteras via SQL Server Management Studio, som körs från en lokal dator som är konfigurerade för åtkomst till Azure SQL Database via en säker VPN eller ExpressRoute-anslutning.

Application Insights är dessutom realtid hantering av programprestanda och analytics via Log Analytics. **Microsoft rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![PaaS-webbprogram för FFIEC referens för Arkitekturdiagram](images/ffiec-paaswa-architecture.png "PaaS webbprogram för Arkitekturdiagram för FFIEC referens")

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Application Gateway
    - Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP
        - Lyssningsport: 443
- Application Insights
- Azure Active Directory
- Azure Application Service-miljö v2
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
- Azure Virtual Network
    - (1) /16 nätverk
    - (4) /24 nätverk
    - Nätverkssäkerhetsgrupper
- Azure-webbapp

## <a name="deployment-architecture"></a>Distributionsarkitektur för

Följande avsnitt beskriver elementen distribution och implementering.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ger kunder möjlighet att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, samordnad åtgärd. Kunder använder en mall för distributionen mallen kan användas för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

**App Service Environment v2**: Azure App Service Environment är en funktion i App Service som innehåller en helt isolerad och dedikerad miljö för säker körning av App Service-program i hög skala. Den här funktionen för isolering krävs för att uppfylla efterlevnadskrav för FFIEC.

App Service-miljöer är isolerade för att endast köra en enda kunds program och de distribueras alltid till ett virtuellt nätverk. Den här funktionen användarisolering kan referensarkitekturen ha fullständig klientisolering, tar bort meddelandet från Azures klientmiljön förbud mot dessa med flera klienter från uppräkning av distribuerade App Service Environment-resurser. Kunderna har detaljerad kontroll över både inkommande och utgående programnätverkstrafik och programmen kan upprätta säkra höghastighetsanslutningar över virtuella nätverk till lokala företagsresurser. Kunderna kan ”Autoskala” med App Service Environment baserat på inläsningsmåtten, tillgängliga budget eller ett definierat schema.

Användning av App Service Environment för den här arkitekturen möjliggör följande konfigurationer:

- Värd i ett skyddat Azure virtuellt nätverk och Nätverkssäkerhetsregler
- Självsignerat intern Azure load balancer certifikat för HTTPS-kommunikation. Som bästa praxis rekommenderar Microsoft användningen av en betrodd certifikatutfärdare för förbättrad säkerhet.
- [Läge för intern belastningsutjämning](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Inaktivera [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändra [TLS-chiffer](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrollen [inkommande trafik N/W portar](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Brandvägg för webbaserade program – begränsa data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Tillåt [Azure SQL Database-trafik](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) ger kunder möjlighet att bygga och agera värd för webbprogram i programmeringsspråket helst utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtual Network

Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. Nätverkssäkerhetsgrupper kan användas för att skydda trafik på ett undernät eller en enskild virtuell dator-nivå. Följande nätverkssäkerhetsgrupper finns:

- 1 nätverkssäkerhetsgruppen för Application Gateway
- 1 nätverkssäkerhetsgruppen för App Service Environment
- 1 nätverkssäkerhetsgruppen för Azure SQL Database
- 1 nätverk säkerhetsgrupp för skyddsmiljö-värd

Var och en av nätverkssäkerhetsgrupperna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje grupp:

- [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
- Log Analytics är ansluten till den [nätverkssäkerhetsgrupp&#39;s diagnostikloggar](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande grupp.

**Azure DNS**: The Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Azure-infrastrukturen. Användare kan som värd för domäner i Azure, hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som för andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör att kunder kan skala programmen och skapa hög tillgänglighet för tjänster. Belastningsutjämnare har stöd för inkommande samt utgående scenarier, och ger med låg fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-program.

### <a name="data-in-transit"></a>Data under överföring

Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av FFIEC.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:

- [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
- [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
- Azure SQL-databasen är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
- [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
- [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper till att identifiera och minska åtkomst till data så att det inte finns databasen via obehörig åtkomst. Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsoft&#39;s flera innehavare molnbaserad katalog- och identity management-tjänsten. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till Azure SQL-databasen.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad behörighet för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisation&#39;s identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisation&#39;s identiteter , och undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet

**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda och komma åt dessa data:

- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en HSM-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Azure Security Center**: med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Azure Application Gateway**: arkitekturen minskar risken för säkerhetsproblem med Azure Application Gateway med en brandvägg för webbaserade program som konfigurerats och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Log Analytics**: de här loggarna konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När samlats in ordnas data i separata tabeller för varje datatyp i Log Analytics-arbetsytor, vilket gör att alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Log Analytics så att kunderna kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Database. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure resurser.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar Application Performance Management-tjänst för webbutvecklare på flera plattformar. Application Insights identifierar prestandaavvikelser och kunder kan använda den för att övervaka det aktiva webbprogrammet. Den inkluderar kraftfulla analysverktyg att hjälpa kunder att diagnostisera problem och förstå vad användare faktiskt gör med appen. Den&#39;s som utformats för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/ffiec-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![PaaS-webbprogram för FFIEC hotmodell](images/ffiec-paaswa-threat-model.png "PaaS webbprogram för FFIEC hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen – FFIEC kundens ansvar matrisen](https://aka.ms/ffiec-crm) visar en lista över alla säkerhetsmål som krävs av FFIEC. Den här matrisen information om implementeringen av varje mål ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen – FFIEC PaaS Web Application implementering Matrix](https://aka.ms/ffiec-paaswa-cim) innehåller information som på vilka FFIEC krav täcks av PaaS webbprogramarkitektur, inklusive detaljerad beskrivningar av hur implementeringen uppfyller kraven för varje skyddad mål.

## <a name="deploy-this-solution"></a>Distribuera den här lösningen
Den här Azure-säkerhet och efterlevnad skissen Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänsten för att distribuera resurser i Azure. Distribution av detaljerade instruktioner finns [här](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Snabbstart
1. Klona eller ladda ned [detta](https://aka.ms/ffiec-paaswa-repo) GitHub-lagringsplatsen till den lokala arbetsstationen.

2. Granska AdministrativeAccountAndPermission.md-0-installationen och kör de angivna kommandona.

3. Distribuera en test-lösning med Contoso exempeldata eller pilot en inledande produktionsmiljö.
    - 1a-ContosoWebStoreDemoAzureResources.ps1
        - Det här skriptet distribuerar Azure-resurser för en demonstration av en webstore med hjälp av Contoso-exempeldata.
    - 1-DeployAndConfigureAzureResources.ps1
        - Det här skriptet distribuerar Azure resurser som behövs för att stödja en produktionsmiljö för ett kundägda webbprogram. Den här miljön bör anpassas ytterligare av kunden utifrån organisationens krav.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute

En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för PaaS web application. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt &quot;tunnel&quot; informationen i en krypterad anslutning mellan kunden&#39;s nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det här är en direkt anslutning av kunden&#39;s-providern telekommunikation data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls &quot;som – är.&quot; Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
- Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
- Kunderna får kopiera och använda det här dokumentet som intern referens.
- Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka en kund&#39;s Azure licens eller prenumerationskostnader.
- Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
- Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
