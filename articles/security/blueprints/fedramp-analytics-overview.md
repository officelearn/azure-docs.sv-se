---
title: Azure-säkerhet och efterlevnad modell - Analytics för FedRAMP
description: Azure-säkerhet och efterlevnad modell - Analytics för FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207118"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure-säkerhet och efterlevnad utkast: Analytics för FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk och auktorisering hanteringsprogram (FedRAMP)](https://www.fedramp.gov/) är ett Förenta staternas myndigheter hela program som ger en standardiserad metod för utvärdering av säkerheten, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure-säkerhet och efterlevnad utkast vägledning för hur man distribuerar en Microsoft Azure analytics arkitektur som hjälper till att implementera en delmängd av FedRAMP hög kontroller. Den här lösningen ger vägledning om distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt som kunder kan använda för att uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund för kunder att Skapa och konfigurera egna Analyslösningar i Azure.

Den här referensen för arkitekturen, associerade kontrollen implementeringsguider och hot modeller är avsedda att fungera som en grund för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är tillräckligt för att helt uppfylla kraven för FedRAMP hög baslinjen. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som FedRAMP-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen ger en analysplattform som kan kunderna skapa sina egna verktyg för analys. Den referens för arkitekturen beskrivs Allmänt användningsfall där kunder indata via bulk data import av SQL-Data administratören eller användningsdata uppdateringar via en operativa användare. Båda två dataströmmar lägga [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) för att importera data till SQL-databasen. Azure Functions måste konfigureras av kund via Azure-portalen för att hantera de importera aktiviteterna som är unik för varje kunds egna analytics krav.

Microsoft Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. den här lösningen innehåller dock Azure Analysis Services tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av kundinformation. Azure Analystjänster är en form av machine learning är avsedda att öka frågan hastigheter genom att identifiera nya relationer mellan datauppsättningar. När data har tränats via flera statistiska funktioner, upp till 7 ytterligare fråga kan pooler (totalt inklusive kundserver 8) synkroniseras med samma tabellmodeller att sprida frågan arbetsbelastning och minska svarstiden.

SQL-databaser kan konfigureras med columnstore-index för förbättrad analys och rapportering. Både Azure Analystjänster och SQL-databaser kan skalas upp eller ned eller stänga av helt som svar på kundens användning. Alla SQL-trafiken krypteras med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

När data överförs till Azure SQL Database och tränats av Azure Analysis Services, är det ned genom både operativa användar- och SQL-Data med Power BI-administratör. Powerbi visar data intuitivt och sammanställer information mellan flera DataSet för att rita större insikter. Dess hög grad av förmåga och enkel integrering med Azure SQL Database gör att kunder kan konfigurera den för att hantera en mängd olika scenarier som krävs av sina affärsbehov.

Hela lösningen bygger på ett Azure Storage som konfigurerar konto kunder från Azure Portal. Azure Storage krypterar alla data med Storage Service-kryptering för att bibehålla sekretessen för data i vila.  Geografiskt Redundant lagring (GRS) säkerställer att en incident på kundens primära Datacenter inte leder till förlust av data som en kopia sparas på en separat plats hundratals mil bort.

Den här arkitekturen hanterar resurser med Azure Active Directory och Azure Key Vault för ökad säkerhet. Systemhälsa övervakas via Operations Management Suite (OMS) och Azure-Monitor. Kunder konfigurera både övervakningstjänster för att avbilda loggar och visa filsystemets hälsa i en enda, enkelt navigera på portalerna tangentbordsgenvägarna instrumentpanel.

Azure SQL Database hanteras vanligtvis via SQL Server Management Studio (SSMS), som körs från en lokal dator som har konfigurerats för åtkomst till Azure SQL Database via en säker VPN eller ExpressRoute-anslutning. **Azure rekommenderar att konfigurera en VPN- eller Azure ExpressRoute-anslutning för hanterings- och import till resursgruppen referens arkitektur.**

![Analytics för FedRAMP referens Arkitekturdiagram](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics för FedRAMP referens Arkitekturdiagram")

### <a name="roles"></a>Roller
Analytics-modell beskriver ett scenario med tre allmänna användartyper: operativa användaren administratör för SQL-Data och system-tekniker. Azure rollbaserad åtkomstkontroll (RBAC) gör det möjligt för implementeringen av exakt åtkomsthantering via inbyggda anpassade roller. Resurser är tillgängliga för att konfigurera [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) och beskriver och implementera [fördefinierade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Systemtekniker
System-tekniker äger kund prenumeration på Azure och konfigurerar distributionen av lösningen på Azure-portalen.

#### <a name="sqldata-administrator"></a>Administratör för SQL-data
SQL-Data administratören upprättar importfunktionen för bulk-data och användningsdata uppdateringsfunktionen för överföring till Azure SQL-databasen. Administratören för SQL-Data är inte ansvarig för eventuella uppdateringar av användningsdata i databasen men kommer att kunna visa data via Power BI.

#### <a name="operational-user"></a>Använd användare
Operativa användaren uppdaterar data regelbundet och äger genereringen av dagliga data. Använd användaren kommer också att tolka resultaten via Power BI.

### <a name="azure-services"></a>Azure-tjänster

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- OMS
- Azure Monitor
- Azure Storage
- ExpressRoute/VPN-Gateway
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Arkitektur för distribution
I följande avsnitt beskrivs element för utveckling och implementering.

![ALT-text](images/fedramp-analytics-components.png?raw=true "Analytics för FedRAMP komponenter diagram")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) är lösningar för att köra små delar av kod i molnet via de flesta programmeringsspråk. Funktioner i den här lösningen integreras med Azure Storage kan automatiskt hämta kundinformation till molnet, vilket underlättar integrering med andra Azure-tjänster. Funktioner är skalbar och innebära en kostnad endast när de körs.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) ger enterprise datamodellering och integrering med data i Azure-plattformstjänster. Azure Analysis Service snabbare gå igenom enorma mängder data genom att kombinera data från flera källor till en enda datamodell.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) ger analytics och rapporteringsfunktioner för kunder som försöker hämtar bättre inblick utanför sitt arbete för databearbetning.

### <a name="networking"></a>Nätverk
**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) ställs in för att hantera trafik dirigeras på distribuerade resurser och tjänster. Nätverkssäkerhetsgrupper är ett neka standardschema och endast tillåta trafik som ingår i den förkonfigurerade åtkomstkontrollista (ACL).

Var och en av de NSG: er har specifika portar och protokoll öppnas så att lösningen fungerar korrekt och på ett säkert sätt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) aktiveras och lagras i ett lagringskonto
  - OMS [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) är ansluten till den NSG diagnostikloggar.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i viloläge med hjälp av kryptering, databasen granskning och andra åtgärder.

**Datareplikering** Azure Government har två alternativ för [datareplikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Standardinställningen för datareplikering är **Geo-Redundant lagring (GRS)**, som lagrar asynkront kundinformation i ett separat Datacenter utanför den primära regionen. Detta säkerställer att återställning av data i en bortfall händelse för primära datacenter.
 - **Lokalt Redundant lagring (LRS)** kan också konfigureras via Azure Storage-konto. LRS replikeras data i en lagringsenhet för skalan som finns i samma region där kunden skapar sitt konto. Alla data replikeras samtidigt, se till att inga säkerhetskopieringsdata försvinner i ett primära lagringsplatsen skala enhet fel.

**Azure Storage** för att uppfylla krypterade data i vila krav alla tjänster som distribueras i den här referensen arkitektur utnyttjar [Azure Storage](https://azure.microsoft.com/services/storage/), som lagrar data med [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för Operativsystemet och datadiskarna. Lösningen integreras med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

**Azure SQL Database** i Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför realtid kryptering och dekryptering av data och loggfilen filer att skydda information i vila. TDE ger försäkran om att lagrade data inte har omfattas obehörig åtkomst.
-   [Regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga data aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering endast klientprogram eller appservrar med tillgång till nycklarna kan komma åt oformaterad text data.
-   [SQL-databas dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter den referens för arkitekturen distribuerar. Kunderna måste du justera inställningar för att uppfylla sina databasschemat för dynamisk datamaskering.

### <a name="logging-and-audit"></a>Loggning och granskning
[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) genererar en all-up visningen av övervakningsdata inklusive aktivitetsloggar, mätvärden och diagnostiska data, aktivera kunder för att skapa en klar bild av systemhälsa.  
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och loggar för kön.
- **Brandväggen loggar**: programmet gatewayen ger fullständig diagnostik och komma åt loggar. Brandväggsloggar är tillgängliga för Programgateway Brandvägg-aktiverade resurser.
- **Logga arkivering**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering med en definierad period 2 dagar. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom ingår följande OMS-lösningar som en del av denna arkitektur:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.

### <a name="identity-management"></a>Identitetshantering
-   Tillämpningen utförs autentiseringen med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure AD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skydda känsliga data i SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory-identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkt incidenter att vidta lämpliga åtgärder kan lösas.
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar fokuserar åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumerationen.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL-databasen finns på [Contoso kurs Demo-programmet](https://github.com/Microsoft/azure-sql-security-sample) exempel.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="expressroute-and-vpn"></a>ExpressRoute- och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för analys av data på ett säkert sätt. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Kunder kan genom att ange korrekt ExpressRoute eller en VPN-anslutning kan lägga till ett lager av dataskydd under överföring.

### <a name="azure-active-directory-setup"></a>Installationsprogram för Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är mycket viktigt att hantera distributionen och etablerar åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till ett befintligt AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
#### <a name="iaas---vm-vonsiderations"></a>IaaS - VM vonsiderations
Den här PaaS-lösningen inte innehålla några Azure IaaS-VM. En kund kan skapa en Azure VM för att köra många av dessa PaaS-tjänster. I det här fallet kan specifika funktioner och tjänster för affärskontinuitet och OMS utnyttjas:

##### <a name="business-continuity"></a>Verksamhetskontinuitet
- **Hög tillgänglighet**: serverarbetsbelastningar grupperas i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% SLA för Azure.

- **Recovery Services-ventilen**: den [Återställningstjänstvalvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) inrymmer säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i Azure i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, aktivera snabbare återställning.

##### <a name="oms"></a>OMS
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

##### <a name="security"></a>Säkerhet
- **Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installeras eller köras på den skyddade virtuella datorer.
- **Hantering av korrigering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten via vilken uppdaterade distributioner kan skapas för korrigering virtuella datorer när de behövs.

#### <a name="azure-commercial"></a>Azure kommersiell
Även om den här arkitekturen för analys av data inte är avsedd för distribution till den [Azure kommersiella](https://azure.microsoft.com/overview/what-is-azure/) miljö, liknande mål kan ske via de tjänster som beskrivs i denna Referensarkitektur samt ytterligare tjänster som är bara tillgängliga i Azure kommersiell miljö. Observera att Azure kommersiella upprätthåller en FedRAMP JAB P-ATO på måttlig effektnivå, vilket gör att myndigheter och partners att distribuera ganska känslig information till molnet genom att använda Azure kommersiell miljö.

Azure kommersiell erbjuder en mängd olika Analystjänster för att ta emot insikter utanför stora mängder data:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), en komponent i den [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), utvecklar en prediktiv analysmodell från en eller flera datakällor. Statistiska funktioner används över flera iterationer för att generera en effektiv modell som program, till exempel Power BI kan använda.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) aktiverar insamlingen av data för alla storlek, typ och införandet hastigheten i en enda plats för drifts- och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data (DFD) för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/blueprintanalyticsthreatmodel) eller finns nedan:

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad
Den [Azure-säkerhet och efterlevnad utkast – FedRAMP hög kunden ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. På liknande sätt den [Azure-säkerhet och efterlevnad utkast – FedRAMP måttlig kunden ansvar matrisen](https://aka.ms/blueprintanalyticscimmod) visar en lista över alla säkerhetskontroller som krävs av FedRAMP måttlig baslinjen. Båda dokumenten innehåller detaljerad information om implementering av varje kontroll är Microsoft, kund, ansvar eller delas mellan två.

Den [Azure-säkerhet och efterlevnad modell - FedRAMP hög kontroll implementering matrisen](https://aka.ms/blueprintanalyticscimhigh) och [Azure-säkerhet och efterlevnad modell - FedRAMP måttlig kontrollen implementering matrisen](https://aka.ms/blueprintanalyticscimmod) ange information som omfattas kontroller av analytics-arkitekturen för varje FedRAMP baslinje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
