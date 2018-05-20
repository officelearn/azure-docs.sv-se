---
title: Azure-säkerhet och efterlevnad modell - Analytics för BNPR
description: Azure-säkerhet och efterlevnad modell - Analytics för BNPR
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 9114dea1f95198b5ff8dc1bf759be5d1179885fd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Azure-säkerhet och efterlevnad utkast: Analytics för BNPR

## <a name="overview"></a>Översikt
Allmänna Data Protection förordning (BNPR) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga data överträdelser och utbilda sekretess personal och andra anställda. BNPR får enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera och analysera personliga data. BNPR inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU), eller att samla in och analysera data som är knutna till Europa boende. BNPR gäller oavsett var en organisation finns.

Microsoft har utvecklat Azure med branschledande säkerhetsåtgärder och sekretesspolicy skydda data i molnet, inklusive kategorier av personuppgifter som identifieras av BNPR. Microsofts [avtalsvillkor](http://aka.ms/Online-Services-Terms) genomför Microsoft kraven för processorer.

Den här Azure-säkerhet och efterlevnad utkast vägledning för att distribuera en analytics-arkitektur för data i Azure som hjälper till med krav på BNPR. Den här lösningen visar hur kunder kan uppfylla vissa säkerhets- och efterlevnadskrav och fungerar som grund att skapa och konfigurera sina egna lösningar för dataanalys i Azure-kunder. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i transporten BNPR kompatibiliteten:
1. Identifiera: Identifiera vilka personuppgifter finns och var den finns.
2. Hantera: Styr hur personuppgifter används och komma åt.
3. Skydda: Upprätta säkerhetsåtgärder för att förebygga, upptäcka och åtgärda säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och bryta mot meddelanden.

Den här referensen för arkitekturen, associerade Implementeringsguide och hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till deras specifika krav och ska inte användas som – i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som BNPR-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen ger en analysplattform som kan kunderna skapa sina egna verktyg för analys. Den referens för arkitekturen beskrivs Allmänt användningsfall där kunder indata via bulk data import av SQL-Data administratören eller användningsdata uppdateringar via en operativa användare. Båda arbetsströmmar införliva Azure Functions för att importera data till Azure SQL Database. Azure Functions måste konfigureras av kund via Azure portal för att hantera de importera aktiviteterna som är unik för varje kunds egna analytics krav.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. den här lösningen innehåller dock Azure Machine Learning tjänster tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av kundinformation. Azure Machine Learning är en form av machine learning är avsedda att öka frågan hastigheter genom att identifiera nya relationer mellan datauppsättningar. När data har tränats via flera statistiska funktioner, upp till 7 ytterligare fråga kan pooler (totalt inklusive kundserver 8) synkroniseras med samma tabellmodeller att sprida frågan arbetsbelastning och minska svarstiden.

Azure SQL-databaser kan konfigureras med columnstore-index för förbättrad analys och rapportering. Både Azure Machine Learning och Azure SQL-databaser kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafiken krypteras med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

När data överförs till Azure SQL Database och tränats av Azure Machine Learning, är det ned genom både operativa användar- och SQL-Data med Power BI-administratör. Powerbi visar data intuitivt och sammanställer information mellan flera DataSet för att rita större insikter. Dess hög grad av förmåga och enkel integrering med Azure SQL Database gör att kunder kan konfigurera den för att hantera en mängd olika scenarier som krävs av sina affärsbehov.

Hela lösningen bygger på Azure Storage som kunder konfigurerar från Azure-portalen. Azure Storage krypterar alla data med Storage Service-kryptering för att bibehålla sekretessen för data i vila. Geografiskt Redundant lagring (GRS) säkerställer att en incident på kundens primära Datacenter inte leder till förlust av data som en kopia sparas på en separat plats hundratals mil bort.

Den här arkitekturen hanterar resurser med Azure Active Directory och Azure Key Vault för ökad säkerhet. Systemhälsa övervakas via Operations Management Suite (OMS) och Azure-Monitor. Kunder konfigurera både övervakningstjänster för att avbilda loggar och visa filsystemets hälsa i en enda, enkelt navigera på portalerna tangentbordsgenvägarna instrumentpanel.

Azure SQL Database hanteras vanligtvis via SQL Server Management Studio (SSMS), som körs från en lokal dator som har konfigurerats för åtkomst till Azure SQL Database via en säker VPN eller ExpressRoute-anslutning. **Azure rekommenderar konfigurera VPN eller ExpressRoute-anslutningen för hanterings- och importera till resursgruppen referens arkitektur**.

![Analytics för BNPR referera Arkitekturdiagram](images/gdpr-analytics-architecture.png?raw=true "Analytics för BNPR referera Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Azure Active Directory
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Monitor
- Azure Storage
- Power BI-instrumentpanel
- Azure Data Catalog
- Azure Security Center
- Application Insights
- Azure Event Grid
- nätverkssäkerhetsgrupper

## <a name="deployment-architecture"></a>Arkitektur för distribution
I följande avsnitt beskrivs distribution och implementeringen elementen.

**Azure händelse rutnätet**
[Azure händelse rutnätet](https://docs.microsoft.com/en-us/azure/event-grid/overview) gör att kunder enkelt kan skapa program med händelsebaserat arkitekturerna. Användare välja Azure resursen som de vill prenumerera på och ge händelsehanteraren eller webhook en slutpunkt för att skicka händelsen till. Kunder kan skydda webhook slutpunkter genom att lägga till frågeparametrar Webhooksadressen när du skapar en händelse-prenumeration. Azure händelse rutnätet har endast stöd för HTTPS webhook-slutpunkter. Azure händelse rutnätet ger kunder möjlighet att styra nivån för olika användare att utföra olika hanteringsåtgärder, till exempel listan händelseprenumerationer, skapa nya och generera nycklar. Händelsen rutnätet använder rollbaserad åtkomstkontroll (RBAC).

**Azure Functions**
[Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) är en server mindre beräknings-tjänst som gör att användarna kan köra kod på begäran utan att behöva etablera uttryckligen eller hantera infrastrukturen. Använd Azure Functions för att köra ett skript eller kod som svar på en rad olika händelser.

**Azure Machine Learning**
[Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/) är en teknik för datavetenskap som gör att datorer kan använda befintliga data för att göra prognoser för framtida beteenden, resultat och trender.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör enkelt identifiera och förstå datakällor för användare som hanterar data. Gemensamma datakällor kan registrerats, märkta och söka efter personlig data. Dina data blir kvar i den befintliga platsen, men en kopia av dess metadata har lagts till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme för 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor (ACL) som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller individuella VM-nivå. Det finns följande NSG: er:
  - En NSG för Active Directory
  - En NSG för arbetsbelastning

Var och en av de NSG: er har specifika portar och protokoll öppnas så att lösningen fungerar korrekt och på ett säkert sätt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) aktiveras och lagras i ett lagringskonto
  - OMS Log Analytics är ansluten till den [NSGS diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal uppstå via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i viloläge med hjälp av kryptering, databasen granskning och andra åtgärder.

**Azure Storage** att uppfylla krypterade data på övriga krav, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda och skydda personuppgifter för att stödja organisationens säkerhetsåtaganden och efterföljandekrav som definieras av BNPR.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för datadiskar. Lösningen integreras med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

**Azure SQL Database**: I Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   Azure SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger försäkran som lagras i personliga data inte har omfattas obehörig åtkomst.
-   [Regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Alltid krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känslig personlig information aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering bara klientprogram eller programservrar med tillgång till nycklarna kan komma åt oformaterad text data.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av registrerade, som tillåter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att bearbetningen av associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att avbryta bearbetningen.
- [SQL dynamiska Data maskering (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att kombinera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och föreslår lämpliga masker som ska användas. Detta hjälper med identifiering av personliga uppgifter som är kvalificerade för BNPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder behöver justera DDM för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker innehåller funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till Azure SQL Database.
-   Tillämpningen utförs autentiseringen med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skydda känsliga data i SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter att bevilja endast åtkomstnivå som användare måste utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer endast vissa åtgärder för att komma åt personliga data. Prenumerationen åtkomst begränsas till administratör för prenumerationen.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har tillgång till viss information, till exempel personuppgifter.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att tillämpa på begäran, just-in-time administrativ åtkomst vid behov.
-   [AAD identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder kan lösas.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter** lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälpa kunderna skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på grundval av behov.
- Key Vault åtkomstprinciper definieras med minsta behörighet till nycklar och hemligheter.
- Alla nycklar och hemligheter i Nyckelvalvet ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade av särskilda maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med RBAC.
- Diagnostik-loggarna för Key Vault aktiveras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklarna är begränsade till de som krävs.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom öppnar Azure Security Center befintliga konfigurationen av Azure-tjänster att ge konfiguration och tjänsterekommendationer för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) för alla upptäckta hot mot hjälpa incidenter team undersöka och åtgärda hot.

### <a name="logging-and-auditing"></a>Granskning och loggning

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att bestämma en åtgärd initieraren tid på förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och loggar för kön.
- **Arkivering av loggen**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering med en definierad period 2 dagar. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom ingår följande OMS-lösningar som en del av denna arkitektur:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

**Azure-Monitor**
[Azure-Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) hjälper kunder att spåra prestanda, upprätthålla säkerheten och identifiera trender genom att aktivera organisationer att granska, skapa aviseringar och arkivera data, inklusive spårning av API anropar i kundernas Azure-resurser.

**Application Insights**
[Programinsikter](https://docs.microsoft.com/en-us/azure/application-insights/) är en utökningsbar Management-program (APM)-tjänst för webbutvecklare på flera plattformar. Du kan använda den för att övervaka live webbprogram. Den identifierar prestandaavvikelser och innehåller kraftfulla analytics verktyg för att diagnostisera problem och vill veta vad användarna faktiskt göra med appen. Den har utformats för att hjälpa användare att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/gdprAnalyticsdfd) eller finns nedan. Den här modellen hjälper kunder att förstå punkterna i risken i infrastrukturen för system när ändringar görs.

![Analytics för BNPR hot modellen](images/gdpr-analytics-threat-model.png?raw=true "Analytics för BNPR hot modellen")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad
Den [Azure-säkerhet och efterlevnad utkast – BNPR kunden ansvar matrisen](https://aka.ms/gdprCRM) visar domänkontrollant och processor ansvar för alla BNPR artiklar. Observera att för Azure-tjänster, en kund är vanligen kontrollanten och Microsoft fungerar som processorn.

Den [Azure-säkerhet och efterlevnad modell - BNPR Data Analytics implementering matrisen](https://aka.ms/gdprAnalytics) innehåller information om vilka BNPR artiklar med hjälp av data analytics arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN- och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av dessa data på ett säkert sätt analytics referera arkitektur. Kunder kan genom på lämpligt sätt att skapa en VPN eller ExpressRoute, lägga till ett lager av dataskydd under överföring.

Genom att implementera en säker VPN-tunnel med Azure, kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure Virtual Network. Den här anslutningen sker via Internet och ger kunder möjlighet att på ett säkert sätt ”tunnel” information i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Den [IPSec-tunnelläge](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism för.

Eftersom trafiken i VPN-tunnel passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutning. Azure ExpressRoute är en fast WAN länken mellan Azure och en lokal plats eller en värdleverantör för Exchange. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Dessutom eftersom det här är en direkt anslutning av kundens telekommunikation providern data överföras inte via Internet och därför visas inte för den.

Bästa praxis för att implementera en säker hybrid-nätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Extrahera transformeringen inläsning (ETL) process
[PolyBase](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/polybase-guide) kan läsa in data till Azure SQL-databas utan att behöva en separat ETL eller importera verktyget. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts affärsinformation och analys stacken, samt fristående verktyg som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Installationsprogram för Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) är mycket viktigt att hantera distributionen och etablerar åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till ett befintligt AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
