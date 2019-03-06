---
title: Azure-säkerhet och efterlevnad skissen - analys för FedRAMP
description: Azure-säkerhet och efterlevnad skissen - analys för FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451883"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure Blueprint för säkerhet och efterlevnad: Analytics for FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) är ett program för USA regeringen som ger en standardiserad metod för säkerhetsbedömningar, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure Blueprint för säkerhet och efterlevnad innehåller anvisningar för hur man distribuerar en Microsoft Azure analytics-arkitektur som hjälper dig att implementera en delmängd av FedRAMP High-kontroller. Den här lösningen innehåller råd om distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att Skapa och konfigurera sina egna lösningar för analys i Azure.

Den här referensarkitekturen och tillhörande kontrollen implementeringsguider hotmodeller är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Distribuera ett program i den här miljön utan ändringar är tillräckligt för att helt uppfylla kraven för FedRAMP High-baslinje. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett sätt som FedRAMP-kompatibla.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen ger en analysplattform som kan kunderna skapa sina egna analysverktyg. Referensarkitekturen beskrivs ett allmänt användningsfall där kunderna indata antingen via bulk dataimporter SQL/Data administratör eller driftdata uppdateringar via en Operational användare. Båda två dataströmmar införliva [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) för import av data till SQL-databasen. Azure Functions måste konfigureras av kunden via Azure Portal för att hantera de import-uppgifterna som är unika för varje kunds egna analytics krav.

Microsoft Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. den här lösningen omfattar dock Azure Analysis Services tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av kunddata. Azure Analytics-tjänster är en typ av machine learning är avsedd att öka fråga hastigheter genom att identifiera nya relationer mellan datauppsättningar. När data har tränats via flera statistikfunktioner, upp till 7 ytterligare fråga kan pooler (8 totalt, inklusive den kund-servern) synkroniseras med samma tabellmodeller att sprida fråga arbetsbelastning och minska svarstider.

SQL-databaser kan konfigureras med columnstore-index för förbättrad analys och rapportering. Både Azure-tjänster för analys och SQL-databaser kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

När data överförs till Azure SQL Database och tränas av Azure Analysis Services, är det ned genom både operativa användar- och administratör för SQL-Data med Power BI. Powerbi visar data intuitivt och sammanställer information över flera datauppsättningar för att rita bättre insikt. Dess hög grad av förmåga och enkel integrering med Azure SQL Database gör att kunder kan konfigurera den för att hantera en mängd olika scenarier som krävs av sina affärsbehov.

Hela lösningen bygger på Azure Storage som konfigurerar konto kunder från Azure Portal. Azure Storage krypterar alla data med Storage Service Encryption för att upprätthålla sekretessen för data i vila.  Geografiskt Redundant lagring (GRS) säkerställer att en incident på kundens primära Datacenter inte leder till förlust av data som en andra kopia kommer att lagras i en separat plats hundratals mil bort.

Den här arkitekturen hanterar resurser med Azure Active Directory och Azure Key Vault för ökad säkerhet. Systemhälsa övervakas via Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.

Azure SQL-databas är ofta hanteras via SQL Server Management Studio (SSMS), som körs från en lokal dator som är konfigurerade för åtkomst till Azure SQL Database via en säker VPN eller ExpressRoute-anslutning. **Azure rekommenderar att du konfigurerar en VPN eller Azure ExpressRoute-anslutning för hantering och import till resursgruppen referens arkitektur.**

![Analytics för FedRAMP-referens för Arkitekturdiagram](images/fedramp-analytics-reference-architecture.png?raw=true "analys för Arkitekturdiagram för FedRAMP-referens")

### <a name="roles"></a>Roller
Analytics-skissen beskriver ett scenario med tre allmänna användartyper: Operational användaren och SQL/Data administratören Systems Engineer. Azure rollbaserad åtkomstkontroll (RBAC) gör det möjligt för implementeringen av exakt åtkomsthantering via inbyggda anpassade roller. Resurser är tillgängliga för att konfigurera [Role-based Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) och beskriver och implementera [fördefinierade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Systemtekniker
Systems Engineer äger kundens prenumeration på Azure och konfigurerar distributionen av lösningen via Azure Portal.

#### <a name="sqldata-administrator"></a>Administratör för SQL-data
SQL-Data-administratören etablerar importfunktionen för bulk-data och funktionen driftdata uppdatera för att ladda upp till Azure SQL-databasen. SQL-Data-administratören ansvarar inte för eventuella driftdata uppdateringar i databasen, men kommer att kunna visa data via Power BI.

#### <a name="operational-user"></a>Operativa användare
Operativa användaren uppdaterar data regelbundet och äger genereringen av dagliga data. Operativa användaren kommer också att tolka resultaten via Power BI.

### <a name="azure-services"></a>Azure-tjänster

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (loggar)
- Azure Storage
- ExpressRoute/VPN-Gateway
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Distributionsarkitektur för
I följande avsnitt finns element för utveckling och implementering.

![alternativ text](images/fedramp-analytics-components.png?raw=true "analys för FedRAMP komponenter diagram")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) finns lösningar för att köra små kodbitar i molnet via de flesta programmeringsspråk. Funktioner i den här lösningen integreras med Azure Storage för att automatiskt hämta kunddata till molnet, underlätta integrering med andra Azure-tjänster. Funktioner är lätt att skala och bara resultera i en kostnader när de körs.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) tillhandahåller enterprise datamodellering och integrering med Azure-dataplattformstjänsterna. Azure Analysis Service påskyndar omkring enorma mängder data genom att kombinera data från flera källor i en enda datamodell.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) ger analytics och rapporteringsfunktioner för kunder som försöker hämta bättre insikt från sina bearbetningar.

### <a name="networking"></a>Nätverk
**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) ställs in för att hantera trafik riktat mot distribuerade resurser och tjänster. Nätverkssäkerhetsgrupper är inställda på ett neka standardschema och endast tillåta trafik som ingår i den förkonfigurerade åtkomstkontrollistan (ACL).

Var och en av NSG: erna har specifika portar och protokoll som är öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - [Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) är ansluten till NSG-diagnostikloggar.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Datareplikering** Azure Government har två alternativ för [datareplikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Standardinställningen för replikering av data är **Geo-Redundant lagring (GRS)**, som lagrar kunddata asynkront i ett separat Datacenter utanför den primära regionen. Detta säkerställer att återställning av data i ett strömavbrott evenemang för det primära datacentralen.
 - **Lokalt Redundant lagring (LRS)** kan också konfigureras via Azure Storage-konto. LRS replikeras data i en lagringsskalningsenhet som ligger i samma region där kunden skapar sitt konto. Alla data replikeras samtidigt, se till att inga säkerhetskopierade data förloras primärlagring skala enhet fel.

**Azure Storage** för att uppfylla krypterade data vid rest krav, alla tjänster som distribueras i den här referensen arkitektur utnyttjar [Azure Storage](https://azure.microsoft.com/services/storage/), som lagrar data med [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database** Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av data och loggfiler att skydda information i vila. TDE ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt data i klartext.
-   [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter referensarkitekturen distribuerar. Kunder kommer att behöva justera dynamisk datamaskning inställningar för att uppfylla sina databasschemat.

### <a name="logging-and-audit"></a>Loggning och granskning
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) genererar en fulla visningen av övervakning av data, inklusive aktivitetsloggar, mått och diagnostiska data, vilket innebär att skapa en klar bild av systemhälsan.  
[Azure Monitor-loggar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet samt filsystemets hälsa. Den samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och köloggar.
- **Brandväggen loggar**: Application Gateway ger fullständig diagnostik och komma åt loggar. Brandväggsloggar är tillgängliga för WAF-aktiverade Application Gateway-resurser.
- **Arkivera loggen**: Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering med en definierad period av 2 dagar. Dessa loggar ansluta till Azure Monitor-loggar för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom kan ingår följande övervakningslösningar som en del av den här arkitekturen:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Instrumentpanelen för säkerhet och granskning ger en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

### <a name="identity-management"></a>Identitetshantering
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure AD för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar fokuserar åtkomsthantering för Azure. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL Database finns i [programmet Contoso Clinic Demo](https://github.com/Microsoft/azure-sql-security-sample) exemplet.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="expressroute-and-vpn"></a>ExpressRoute och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för analys av data. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Kunder kan genom att på lämpligt sätt att konfigurera ExpressRoute eller VPN kan lägga till ett lager säkerhet för data under överföring.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
#### <a name="iaas---vm-considerations"></a>IaaS - överväganden för virtuella datorer
Det här PaaS-lösning inte inkluderar alla Azure IaaS-VM. En kund kan skapa en virtuell Azure-dator för att köra många av de här PaaS-tjänster. I det här fallet kan specifika funktioner och tjänster för affärskontinuitet och Azure Monitor-loggar utnyttjas:

##### <a name="business-continuity"></a>Verksamhetskontinuitet
- **Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

- **Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

##### <a name="monitoring-solutions"></a>Övervakningslösningar
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Lösningen för uppdateringshantering kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking): Lösningen ändringsspårning kan kunder enkelt kan identifiera ändringar i miljön.

##### <a name="security"></a>Säkerhet
- **Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.
- **Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

#### <a name="azure-commercial"></a>Azure kommersiell
Även om den här arkitekturen för analys av data inte är avsedd för distribution till den [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) miljö, liknande kan ske via tjänster som beskrivs i denna Referensarkitektur samt ytterligare tjänster som är endast tillgängliga i Azure kommersiell miljö. Observera att Azure Commercial finns kvar en FedRAMP JAB P-ATO med begränsad inverkan, vilket gör att myndigheter och partner att distribuera mindre känslig information till molnet att utnyttja Azure kommersiell miljö.

Azure kommersiell erbjuder en mängd olika tjänster för analys för att hämta insikter från stora mängder data:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), en komponent i den [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), utvecklar en förutsägande analysmodell från en eller flera datakällor. Statistikfunktioner används över flera iterationer för att generera en effektiv modell som program, till exempel Power BI kan sedan använda.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) möjliggör insamlingen av data av alla storlekar, typer och datainmatning i en enda plats för drifts- och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram (DFD) för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/blueprintanalyticsthreatmodel) eller finns nedan:

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure Security Blueprint och efterlevnad – FedRAMP hög kundens ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP High-baslinje. På samma sätt kan den [Azure Security Blueprint och efterlevnad – FedRAMP måttlig kundens ansvar matrisen](https://aka.ms/blueprintanalyticscimmod) visar en lista över alla säkerhetskontroller som krävs av FedRAMP måttlig baslinjen. Både dokument detaljer om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - FedRAMP hög kontroll implementering matrisen](https://aka.ms/blueprintanalyticscimhigh) och [Azure säkerhet och efterlevnad skissen - FedRAMP begränsad kontroll implementering matrisen](https://aka.ms/blueprintanalyticscimmod) ger information som omfattas kontroller av analytics-arkitekturen för varje FedRAMP-baslinje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
