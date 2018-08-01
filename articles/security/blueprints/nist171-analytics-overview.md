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
ms.openlocfilehash: b2c96509b2b8fd61c1814d5b4a015048eeb1807d
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391790"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - dataanalys för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera en arkitektur för analys av data i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visar hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav och fungerar som en grund för kunder att skapa och konfigurera sina egna lösningar för dataanalys i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och bör inte användas som-är i en produktionsmiljö. Distribuera den här arkitekturen utan ändringar är tillräckligt för att uppfylla kraven för SP NIST 800-171 helt. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen ger en analysplattform som kan kunderna skapa sina egna analysverktyg. Referensarkitekturen beskrivs ett allmänt användningsfall där kunderna indata antingen via bulk dataimporter SQL/Data administratör eller driftdata uppdateringar via en Operational användare. Båda workstreams införliva Azure Functions för att importera data till Azure SQL Database. Azure Functions måste konfigureras av kunden via Azure portal för att hantera de import uppgifterna som är unika för analytics kundbehov.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. den här lösningen omfattar dock Azure Machine Learning services tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av data. Azure Machine Learning är en typ av machine learning är avsedd att öka fråga hastigheter genom att identifiera nya relationer mellan datauppsättningar. När data har tränats via flera statistikfunktioner, upp till 7 ytterligare fråga kan pooler (8 totalt, inklusive den kund-servern) synkroniseras med samma tabellmodeller att sprida fråga arbetsbelastning och minska svarstider.

Azure SQL Database kan konfigureras med columnstore-index för förbättrad analys och rapportering. Både Azure Machine Learning och Azure SQL Database kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

När data överförs till Azure SQL Database och tränas genom Azure Machine Learning kan det bryts ned av både den operativa användare och administratör för SQL-Data med Power BI. Powerbi visar data intuitivt och sammanställer information över flera datauppsättningar för att rita bättre insikt. Dess hög grad av förmåga och enkel integrering med Azure SQL Database gör att kunder kan konfigurera den för att hantera en mängd olika scenarier som krävs av sina affärsbehov.

Hela lösningen bygger på Azure Storage, som kunder konfigurera från Azure-portalen. Azure Storage krypterar alla data med Storage Service Encryption för att upprätthålla sekretessen för data i vila. Geografiskt redundant lagring säkerställer att en incident på kundens primära Datacenter inte leder till förlust av data, som en andra kopia kommer att lagras i en separat plats hundratals mil bort.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.

Azure SQL-databas är ofta hanteras via SQL Server Management Studio, som körs från en lokal dator som är konfigurerade för åtkomst till Azure SQL Database via en säker VPN eller ExpressRoute-anslutning. **Microsoft rekommenderar att konfigurera en VPN eller ExpressRoute-anslutning för hantering och importera till resursgruppen**.

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
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 nätverk
    - (2) /24 nätverk
    - (2) nätverkssäkerhetsgrupper
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) kan kunderna enkelt skapa program med händelsebaserade arkitekturer. Användarna välja Azure-resursen de vill prenumerera på och ge händelsehanterare eller webhook en slutpunkt för att skicka händelsen till. Kunder kan skydda webhook slutpunkter genom att lägga till frågeparametrar webhook-URL när du skapar en händelseprenumeration. Azure Event Grid har endast stöd för HTTPS webhook-slutpunkter. Azure Event Grid kan kunderna styra för olika användare att utföra olika hanteringsåtgärder, till exempel listan händelseprenumerationer, skapa nya och generera nycklar. Event Grid använder rollbaserad åtkomstkontroll i Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) är en server utan beräkningstjänst som gör det möjligt för användarna att köra kod på begäran utan att behöva installera eller hantera infrastruktur. Använd Azure Functions för att köra ett skript eller kod som svar på en rad olika händelser.

**Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) är en datavetenskapsteknik som gör att datorer kan använda befintliga data till att prognostisera framtida beteenden, resultat och trender.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör datakällor enkelt identifiera och förstå hur av de användare som hanterar data. Gemensamma datakällor kan registrerats, taggade och söka efter data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. Nätverkssäkerhetsgrupper kan användas för att skydda trafik på ett undernät eller en enskild virtuell dator-nivå. Följande nätverkssäkerhetsgrupper finns:
  - En nätverkssäkerhetsgrupp för Active Directory
  - En nätverkssäkerhetsgrupp för arbetsbelastning

Var och en av nätverkssäkerhetsgrupperna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje grupp:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Log Analytics är ansluten till den [network security group diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande grupp.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner till Azure Storage via Azure portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av SP NIST 800-171.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   Azure SQL-databasen är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till Azure SQL Database.
-   Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information, till exempel data.  Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvara security moduler skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Azure Security Center**: med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Log Analytics**: de här loggarna konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När samlats in ordnas data i separata tabeller för varje datatyp i Operations Management Suite-arbetsytor, vilket gör att alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Log Analytics så att kunderna kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Database. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure resurser.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utökningsbar Application Performance Management-tjänst för webbutvecklare på flera plattformar. Den identifierar prestandaavvikelser och inkluderar kraftfulla analysverktyg för att diagnostisera problem och förstå vad användarna gör med appen. Det är utformat för att hjälpa användare att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-analytics-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![Dataanalys för SP NIST 800-171 hotmodell](images/nist171-analytics-threat-model.png "dataanalys för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 Data Analytics kontroll implementering matrisen](https://aka.ms/nist171-analytics-cim) innehåller information som på vilka SP NIST 800-171-kontroller med hjälp av data analytics-arkitekturen, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av dessa data analytics referensarkitekturen. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Process för extrahering, transformering och laddning
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data till Azure SQL Database utan att behöva en separat extrahera, transformera, läsa in eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsofts business intelligence och analys stack, samt från tredje part-verktyg som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig Azure Active Directory genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en Azure Active Directory-skog.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
