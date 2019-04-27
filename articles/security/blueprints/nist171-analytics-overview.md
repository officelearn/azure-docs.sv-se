---
title: Azure-säkerhet och efterlevnad skissen - dataanalys för SP NIST 800-171
description: Azure-säkerhet och efterlevnad skissen - dataanalys för SP NIST 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608847"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - dataanalys för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera en arkitektur för analys av data i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visas hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav. Det fungerar också som en grund för kunder att skapa och konfigurera sina egna lösningar för dataanalys i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav. De bör inte användas som-är i en produktionsmiljö. Distribuera den här arkitekturen utan ändringar är tillräckligt för att uppfylla kraven för SP NIST 800-171 helt. Kunderna ansvarar för att utföra lämplig säkerhet och efterlevnad utvärderingar av valfri lösning som använder den här arkitekturen. Kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen ger en analysplattform som kan kunderna skapa sina egna analysverktyg. Referensarkitekturen beskriver allmänna användningsfall. Kunder kan använda den för att mata in data via bulk dataimporter av administratören för SQL-Data. De kan också använda det att mata in data med hjälp av driftdata uppdateringar via en operational användare. Båda workstreams införliva Azure Functions för att importera data till Azure SQL Database. Azure Functions måste konfigureras av kunden via Azure portal för att hantera de import uppgifterna som är unika för analytics kundbehov.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen använder Azure Machine Learning-tjänster och SQL-databas för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av data. Machine Learning är avsedd att öka fråga hastigheter genom att identifiera nya relationer mellan datauppsättningar. Första gången tränas data via flera statistikfunktioner. Upp till sju ytterligare fråga kan därefter pooler synkroniseras med samma tabellmodeller att sprida fråga arbetsbelastning och minska svarstider. Kundserver ger summan av frågan pooler till åtta.

SQL-databas kan konfigureras med columnstore-index för förbättrad analys och rapportering. Machine Learning och SQL-databas kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar vi att använda en betrodd certifikatutfärdare för förbättrad säkerhet.

När data överförs till SQL-databasen och tränas genom Machine Learning, är det ned genom den operativa användar- och SQL-Data med Power BI. Powerbi visar data intuitivt och sammanställer information över flera datauppsättningar för att rita bättre insikt. Powerbi har en hög grad av förmåga och kan enkelt integreras med SQL-databas. Kunder kan konfigurera det för att hantera en mängd olika scenarier som krävs av sina affärsbehov.

Hela lösningen bygger på Azure Storage, som kunder konfigurera från Azure-portalen. Storage krypteras alla data med Storage Service Encryption för att upprätthålla sekretessen för data i vila. GEO-redundant lagring säkerställer att en incident på kundens primära Datacenter inte resultera i en förlust av data. En andra kopia lagras i en separat plats hundratals mil bort.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory (Azure AD) rollbaserad åtkomstkontroll (RBAC) används för att styra åtkomst till distribuerade resurser. Dessa resurser inkluderar Kundnycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar. Platssystemets hälsostatus visas i en enda instrumentpanel som är lätt att använda.

SQL-databas är ofta hanteras via SQL Server Management Studio. Den körs från en lokal dator som är konfigurerade för åtkomst till SQL-databas via en säker VPN eller Azure ExpressRoute-anslutning. *Vi rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till resursgruppen*.

![Dataanalys för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-analytics-architecture.png "dataanalys för Arkitekturdiagram för SP NIST 800-171-referens")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (loggar)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 nätverk
    - (2) /24 nätverk
    - (2) nätverkssäkerhetsgrupper
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Azure Event Grid**: Med [Event Grid](https://docs.microsoft.com/azure/event-grid/overview), kunder kan enkelt skapa program med händelsebaserade arkitekturer. Användarna välja Azure-resursen ska prenumerera på. De ger sedan händelsehanterare eller webhook en slutpunkt för att skicka händelsen till. Kunder kan skydda webhook slutpunkter genom att lägga till frågeparametrar Webhooksadressen när de skapar en händelseprenumeration. Event Grid stöder endast webhook för HTTPS-slutpunkter. Med Event Grid kan kunderna styra åtkomstnivån som ges till olika användare att utföra olika hanteringsåtgärder. Användare kan visa en lista över prenumerationer på händelser, skapa nya och generera nycklar. Event Grid använder Azure RBAC.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) är en serverlös beräkningstjänst som kör kod på begäran. Du behöver installera eller hantera infrastruktur. Använd Azure Functions för att köra ett skript eller kod som svar på en rad olika händelser.

**Azure Machine Learning-tjänsten**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) är en datavetenskapsteknik som gör att datorer kan använda befintliga data till att prognostisera framtida beteenden, resultat och trender.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör det enkelt att identifiera och förstå av de användare som hanterar data datakällor. Gemensamma datakällor kan registrerats, taggade och söka efter data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog. En referens till datakällans plats ingår. Metadata indexeras för att göra det lätt att identifiera via sökningsfunktionen varje datakälla. Indexering gör det också att förstå att användare som identifierar den.

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller en enskild virtuell dator-nivå. Följande Nätverkssäkerhetsgrupper finns:
  - En NSG för Active Directory
  - En NSG för arbetsbelastning

Varje NSG: erna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Azure Monitor-loggar är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerad med dess motsvarande NSG.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Storage via Azure portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: Att uppfylla krav för krypterade data i vila, alla [Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Den här funktionen kan du skydda och skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av SP NIST 800-171.

**Azure Disk Encryption**: [Diskkryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) använder BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: SQL Database-instansen använder följande säkerhetsåtgärder för databasen:
-   [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Det fungerar i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger assurance som lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker. Det ger säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När datakryptering är aktiverat, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt oformaterad text data.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till replikdata användare eller program. Det kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Dynamisk datamaskning hjälper till att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. *Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsoft multitenant molnbaserade företagskatalogen och identitetshanteringen hanteringstjänsten. Alla användare för den här lösningen skapas i Azure AD och innehåller de användare som har åtkomst till SQL-databas.
-   Autentisering till programmet utförs med hjälp av Azure AD. Läs mer om hur du [integrera program med Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kolumnen databaskryptering använder också Azure AD för att autentisera program till SQL-databas. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kan användas av administratörer för att definiera detaljerade behörigheter. Med den, kan de bevilja endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) kan användas av kunder för att minimera antalet användare som har åtkomst till viss information, till exempel data. Administratörer kan använda Azure AD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella sårbarheter som påverkar organisationens identiteter. Konfigurerar den automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter. Det kan också undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault kan du skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvara security-modul-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Azure Security Center**: Med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Security Center har även åtkomst till befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

 Security Center använder en mängd funktioner och meddela kunder om eventuella attacker som är inriktade på sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller misstänkt aktivitet inträffar. Kunder kan använda [anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

 Security Center tillhandahåller rangordnade säkerhetsaviseringar och incidenter. Security Center gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot. Incidenter team kan använda rapporterna när de undersöka och åtgärda hot.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Storage-loggar, granskningsloggar för Key Vault och Azure Application Gateway-loggar för åtkomst och brandväggen. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar att uppfylla deras specifika krav.

**Azure Monitor-loggar**: Loggar konsolideras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp i Log Analytics-arbetsytor. På så sätt kan analyseras alla data tillsammans oavsett originalkälla. Security Center integrerar med Azure Monitor-loggar. Kunder kan använda Kusto-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Azure [övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Det ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Lösningen SQL-hälsokontroll bedömer risken och hälsotillståndet i server-miljöer med regelbundna intervall. Tjänsten ger kunderna med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning. Rapporteras även hur många agenter inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från SQL-databas. Kunder kan använda Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösning lätt kan identifiera ändringar i miljön.

**Azure Monitor**: [Övervaka](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utökningsbar Application Performance Management-tjänst för webbutvecklare på flera plattformar. Den identifierar prestandaavvikelser och inkluderar kraftfulla analysverktyg. Verktyg att diagnostisera problem och hjälpa kunderna att förstå vad användarna gör med appen. Det är utformat för att hjälpa användare att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-analytics-tm) eller hittar du här. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i infrastrukturen för system när de gör ändringar.

![Dataanalys för SP NIST 800-171 hotmodell](images/nist171-analytics-threat-model.png "dataanalys för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 Data Analytics kontroll implementering matrisen](https://aka.ms/nist171-analytics-cim) innehåller information som på vilka SP NIST 800-171-kontroller med hjälp av data analytics-arkitekturen. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för analys av data. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen sker över Internet. Kunder kan använda anslutningen till på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan sina nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom VPN-tunneln trafik passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat anslutningsalternativ för ännu mer säker. ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. ExpressRoute-anslutningar ansluta direkt till en kunds telekommunikation providern. Därför kan överföras inte via Internet data och visas inte. De här anslutningarna erbjuder mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar. 

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Process för extrahering, transformering och laddning
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i SQL-databas utan att behöva en separat extrahering, transformering och laddning eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsoft business intelligence och analys stack och verktyg från tredje part som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-ad-setup"></a>Konfigurationen av Azure AD
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. Lokala Active Directory kan integreras med Azure AD med [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till Azure AD. Gör detta genom att kontrollera distribuerade Active Directory-infrastruktur en underdomän till en Azure AD-skog.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
