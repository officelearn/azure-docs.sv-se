---
title: Handlingsplan för säkerhet och efterlevnad i Azure-analys för FedRAMP
description: Handlingsplan för säkerhet och efterlevnad i Azure-analys för FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "71257594"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Handlingsplan för säkerhet och efterlevnad i Azure: analys för FedRAMP

## <a name="overview"></a>Översikt

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) är ett USA myndighets omfattande program som ger en standardiserad metod för säkerhetsbedömning, auktorisering och kontinuerlig övervakning av moln produkter och tjänster. Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning om hur du levererar en Microsoft Azure Analytics-arkitektur som hjälper till att implementera en delmängd av FedRAMP-hög kontroller. Den här lösningen ger vägledning om distribution och konfiguration av Azure-resurser för en gemensam referens arkitektur som demonstrerar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad och fungerar som en grund för kunderna att Bygg och konfigurera egna analys lösningar i Azure.

Den här referens arkitekturen, de tillhör ande guiderna för kontroll implementering och hot modeller är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav och bör inte användas i en produktions miljö. Det räcker inte att distribuera ett program i den här miljön utan att ändra kraven i FedRAMP hög bas linje. Observera följande:
- Arkitekturen ger till gång till en bas linje som hjälper kunder att distribuera arbets belastningar till Azure på ett FedRAMP-kompatibelt sätt.
- Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här lösningen ger en analys plattform som kunder kan bygga egna analys verktyg på. Referens arkitekturen beskriver ett allmänt användnings fall där kunder matar in data via Mass data import av SQL/data-administratören eller genom drift data uppdateringar via en drift användare. Båda arbets strömmarna innehåller [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) för att importera data till SQL Database. Azure Functions måste konfigureras av kunden via Azure Portal för att hantera de import aktiviteter som är unika för varje kunds behov av analys.

Microsoft Azure erbjuder en rad olika rapporterings-och analys tjänster för kunden. den här lösningen införlivar dock Azure Analysis Services tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering av kunddata. Azure Analytics-tjänster är en form av Machine Learning som är avsedd att öka frågans hastighet genom att identifiera nya relationer mellan data uppsättningar. När data har tränats genom flera statistiska funktioner kan upp till 7 ytterligare lagringspooler (8 totalt inklusive kund servern) synkroniseras med samma tabell modeller för att sprida frågor till arbets belastningen och minska svars tiderna.

För förbättrad analys och rapportering kan SQL-databaser konfigureras med columnstore-index. Både Azure Analytics-tjänster och SQL-databaser kan skalas upp eller ned eller stängas av fullständigt som svar på kund användning. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

När data har laddats upp till Azure SQL Database och tränas av Azure Analysis Services, sammanfattas de av både den operativa användaren och SQL/data-administratören med Power BI. Power BI visar data intuitivt och drar samman information över flera data uppsättningar för att få bättre insikt. Den höga graden av anpassning och enkel integrering med Azure SQL Database garanterar att kunderna kan konfigurera den för att hantera en mängd olika scenarier som krävs av deras affärs behov.

Hela lösningen bygger på en Azure Storage som konto kunder konfigurerar från Azure-portalen. Azure Storage krypterar alla data med Kryptering för lagringstjänst för att bevara konfidentialiteten för data i vila.  Geografiskt redundant lagring (GRS) garanterar att en negativ händelse på kundens primära Data Center inte leder till förlust av data eftersom en andra kopia kommer att lagras på en annan plats hundratals mil.

Den här arkitekturen hanterar resurser med Azure Active Directory och Azure Key Vault för förbättrad säkerhet. System hälsan övervakas via Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel.

Azure SQL Database hanteras ofta via SQL Server Management Studio (SSMS), som körs från en lokal dator som har kon figurer ATS för att få åtkomst till Azure SQL Database via en säker VPN-eller ExpressRoute-anslutning. **Azure rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till resurs gruppen referens arkitektur.**

![Analys för FedRAMP referens arkitektur diagram](images/fedramp-analytics-reference-architecture.png?raw=true "Analys för FedRAMP referens arkitektur diagram")

### <a name="roles"></a>Roller
Analys skissen beskriver ett scenario med tre allmänna användar typer: drift användaren, SQL/data-administratören och system teknikern. Med Azure Role-baserade Access Control (RBAC) kan du implementera exakt åtkomst hantering via inbyggda anpassade roller. Resurser är tillgängliga för att konfigurera [rollbaserad Access Control](../../role-based-access-control/role-assignments-portal.md) och för att disponera och implementera [fördefinierade roller](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>System tekniker
System teknikern äger kund prenumerationen för Azure och konfigurerar distributionen av lösningen via Azure Portal.

#### <a name="sqldata-administrator"></a>SQL/data-administratör
SQL/data-administratören skapar funktionen Mass data import och funktionen för användnings data uppdatering för överföring till Azure SQL-databasen. SQL/data-administratören ansvarar inte för några drift data uppdateringar i databasen, men kommer att kunna visa data via Power BI.

#### <a name="operational-user"></a>Drift användare
Den operativa användaren uppdaterar data regelbundet och äger den dagliga data genereringen. Den operativa användaren kommer också att tolka resultat via Power BI.

### <a name="azure-services"></a>Azure-tjänster

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (loggar)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs utvecklings-och implementerings elementen.

![alternativ text](images/fedramp-analytics-components.png?raw=true "Diagram över analys för FedRAMP-komponenter")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) är lösningar för att köra små delar av kod i molnet via de flesta programmeringsspråk. Funktioner i den här lösningen integreras med Azure Storage för att automatiskt hämta Kunddata till molnet, vilket underlättar integrering med andra Azure-tjänster. Funktionerna är enkelt skalbara och innebär bara en kostnad när de körs.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) ger företags data modellering och integrering med Azure Data Platform-tjänster. Azure Analysis Service påskyndar surfningen genom stora mängder data genom att kombinera data från flera källor i en enda data modell.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) ger analys-och rapporterings funktioner för kunder som försöker hämta bättre insikter från sina data bearbetnings ansträngningar.

### <a name="networking"></a>Nätverk
**Nätverks säkerhets grupper**: [NSG: er](../../virtual-network/virtual-network-vnet-plan-design-arm.md) har ställts in för att hantera trafik riktade mot distribuerade resurser och tjänster. Nätverks säkerhets grupper ställs in på ett schema som nekas som standard och tillåter endast trafik som finns i den förkonfigurerade Access Control listan (ACL).

Varje NSG: er har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
  - [Azure Monitor loggar](../../azure-monitor/insights/azure-networking-analytics.md) är anslutna till NSG: s diagnostikloggar.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Datareplikering** Azure Government har två alternativ för [datareplikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Standardinställningen för datareplikering är **Geo-redundant lagring (GRS)** , som asynkront lagrar kund information i ett separat Data Center utanför den primära regionen. Detta säkerställer återställning av data i en total förlust händelse för det primära data centret.
 - **Lokalt Redundant lagring (LRS)** kan alternativt konfigureras via Azure Storage-kontot. LRS replikerar data inom en lagrings skalnings enhet som finns i samma region som kunden skapar sitt konto i. Alla data replikeras samtidigt, vilket säkerställer att inga säkerhets kopierings data går förlorade i en primär enhets skalnings enhets enhets problem.

**Azure Storage** För att möta krypterade data med rest-krav kan alla tjänster som distribueras i denna referens arkitektur använda [Azure Storage](https://azure.microsoft.com/services/storage/), vilket lagrar data med [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md).

**Azure Disk Encryption** 
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database** Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Autentisering och auktorisering i AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   SQL Database har kon figurer ATS för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av data och loggfiler för att skydda information i vila. TDE garanterar att lagrade data inte har blivit föremål för obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller App-servrar med åtkomst till nycklarna komma åt oformaterade data.
-   [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter att referens arkitekturen har distribuerats. Kunderna måste justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="logging-and-audit"></a>Loggning och granskning
[Azure Monitor](../../azure-monitor/overview.md) genererar en all visning av övervaknings data, inklusive aktivitets loggar, Mät värden och diagnostikdata, vilket gör det möjligt för kunderna att skapa en fullständig bild av systemets hälsa.  
[Azure Monitor loggar](../azure-security-disk-encryption-overview.md) innehåller omfattande loggning av system-och användar aktiviteter, samt system hälsan. Den samlar in och analyserar data som genererats av resurser i Azure och lokala miljöer.
- **Aktivitets loggar**: [aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration.
- **Diagnostikloggar**: [diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar omfattar Windows händelse system loggar och Azure Blob Storage, tabeller och Queue-loggar.
- **Brand Väggs loggar**: Application Gateway innehåller fullständiga diagnostik-och åtkomst loggar. Brand Väggs loggar är tillgängliga för WAF-aktiverade Application Gateway-resurser.
- **Logg arkivering**: alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering med en definierad kvarhållningsperiod på 2 dagar. Dessa loggar ansluter till Azure Monitor loggar för bearbetning, lagring och instrument panels rapportering.

Dessutom ingår följande övervaknings lösningar som en del av den här arkitekturen:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation lösning lagrar, kör och hanterar Runbooks.
-   [Säkerhet och granskning](../../security-center/security-center-intro.md): säkerhet och granskning instrument panelen ger en stor inblick i säkerhets läget för resurser genom att tillhandahålla mått på säkerhets domäner, viktiga problem, identifieringar, Hot information och vanliga säkerhets frågor.
-   [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): med SQL-hälsokontroll-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Azure aktivitets loggar](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

### <a name="identity-management"></a>Identitetshantering
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure AD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.
-   Med [Azure Role-baserade Access Control (RBAC)](../../role-based-access-control/role-assignments-portal.md) kan du prioriterad åtkomst hantering för Azure. Åtkomst till prenumerationen är begränsad till prenumerations administratören.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL Database finns i exempel på [demonstrations program för Contoso-lektioner](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="expressroute-and-vpn"></a>ExpressRoute och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribueras som en del av den här referens arkitekturen för data analys. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Genom att konfigurera ExpressRoute eller en VPN-anslutning kan kunder lägga till ett skydds lager för data under överföring.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med AAD i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md). Kunder kan också knyta samman den distribuerade Active Directory infrastrukturen (domänkontrollanter) till en befintlig AAD genom att göra den distribuerade Active Directory-infrastrukturen till en under domän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
#### <a name="iaas---vm-considerations"></a>IaaS – överväganden för virtuella datorer
Den här PaaS-lösningen inkluderar inte några virtuella Azure IaaS-datorer. En kund kan skapa en virtuell Azure-dator för att köra många av dessa PaaS-tjänster. I det här fallet kan specifika funktioner och tjänster för affärs kontinuitet och Azure Monitors loggar utnyttjas:

##### <a name="business-continuity"></a>Kontinuitet i verksamheten
- **Hög tillgänglighet**: Server arbets belastningarna grupperas i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

- **Recovery Services valv**: [Recovery Services valvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhetskopierade data och skyddar alla konfigurationer av Azure-Virtual Machines i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställnings tider.

##### <a name="monitoring-solutions"></a>Övervakningslösningar
-   [AD-utvärdering](../../azure-monitor/insights/ad-assessment.md): den Active Directory hälso kontroll lösningen utvärderar hälso-och hälso tillståndet i Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Utvärdering av program mot skadlig kod](../../security-center/security-center-install-endpoint-protection.md): lösnings rapporter för program mot skadlig kod för skadlig kod, hot och skydds status.
-   [Uppdateringshantering](../../automation/automation-update-management.md): uppdateringshantering-lösningen tillåter kund hantering av säkerhets uppdateringar för operativ system, inklusive status för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Ändringsspårning](../../automation/change-tracking.md): ändringsspårning-lösningen gör det möjligt för kunderna att enkelt identifiera ändringar i miljön.

##### <a name="security"></a>Säkerhet
- **Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för Virtual Machines ger real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara med konfigurerbara aviseringar när kända skadliga eller oönskad program vara försöker installera eller köra på skyddade virtuella datorer.
- **Uppdaterings hantering**: virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.

#### <a name="azure-commercial"></a>Azure-kommersiell
Även om den här data analys arkitekturen inte är avsedd för distribution till den [kommersiella Azure](https://azure.microsoft.com/overview/what-is-azure/) -miljön, kan liknande mål uppnås genom tjänsterna som beskrivs i denna referens arkitektur, samt ytterligare tjänster som är tillgängliga endast i den kommersiella Azure-miljön. Observera att Azure Commercial har en FedRAMP JAB P-ATO på nivån måttlig påverkan, vilket gör det möjligt för myndigheter och partner att distribuera känslig känslig information till molnet som utnyttjar Azures kommersiella miljö.

Azure Commercial erbjuder en mängd olika analys tjänster för att få insikter om stora mängder data:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio)en komponent i [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/)utvecklar en förutsägelse analys modell från en eller flera data källor. Statistiska funktioner används över flera iterationer för att generera en effektiv modell som program som Power BI kan sedan använda.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) möjliggör insamling av data i valfri storlek, typ och inmatnings hastighet på en enda plats för operativa och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet och kan integreras snyggt med andra Azure-tjänster.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet (DFD) för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/blueprintanalyticsthreatmodel) eller kan hittas nedan:

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – FedRAMP hög kund ansvar](https://aka.ms/blueprinthighcrm) visas alla säkerhets kontroller som krävs av den höga bas linjen för FedRAMP. På samma sätt visar [matrisen handlingsplan för säkerhet och efterlevnad i Azure – FedRAMP måttliga kund ansvar](https://aka.ms/blueprintanalyticscimmod) alla säkerhets kontroller som krävs av den FedRAMP måttliga bas linjen. Båda dokumenten anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP för hög kontroll](https://aka.ms/blueprintanalyticscimhigh) och implementering av [handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP för måttlig kontroll](https://aka.ms/blueprintanalyticscimmod) innehåller information om vilka kontroller som omfattas av analys arkitekturen för varje FedRAMP-bas linje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
