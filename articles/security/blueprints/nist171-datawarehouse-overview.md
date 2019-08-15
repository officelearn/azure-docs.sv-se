---
title: Handlingsplan för säkerhet och efterlevnad i Azure-informations lager för NIST SP 800-171
description: Handlingsplan för säkerhet och efterlevnad i Azure-informations lager för NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 98eb3834efa6dc6ce5d53990f4bb530351660b8a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946710"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Handlingsplan för säkerhet och efterlevnad i Azure-informations lager för NIST SP 800-171

## <a name="overview"></a>Översikt
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller rikt linjer för att skydda sekretessbelagda oklassificerade uppgifter (Cui) som finns i icke-federala informations system och organisationer. NIST SP 800-171 fastställer 14 familjer av säkerhets krav för att skydda konfidentialiteten hos CUI.

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för att hjälpa kunder att distribuera en data lager arkitektur i Azure som implementerar en delmängd av NIST SP 800-171-kontroller. Den här lösningen visar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad. Den fungerar också som grund för kunderna att bygga och konfigurera egna data lager lösningar i Azure.

Den här referens arkitekturen, tillhör ande implementerings guide och hot modell är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav. De bör inte användas som de är i en produktions miljö. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här lösningen innehåller en referens arkitektur som implementerar ett informations lager med hög prestanda och säker moln lagring. Det finns två separata data nivåer i den här arkitekturen. En nivå är den plats där data importeras, lagras och mellanlagras i en klustrad SQL-miljö. En annan nivå är för SQL Data Warehouse. Med den här nivån läses data in med hjälp av ett ETL-verktyg (Extract-Transform-Load) (till exempel [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-frågor) för bearbetning. När data har lagrats i SQL Data Warehouse kan analyser köras i en enorm skala.

Azure erbjuder en rad olika rapporterings-och analys tjänster för kunden. Den här lösningen innehåller SQL Server Reporting Services för snabb skapande av rapporter från SQL Data Warehouse. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar vi att du använder en betrodd certifikat utfärdare för förbättrad säkerhet.

Azure SQL Data Warehouse lagrar data i Relations tabeller med kolumn lagring. Det här formatet minskar data lagrings kostnaderna avsevärt samtidigt som frågans prestanda förbättras. Beroende på användnings kraven kan SQL Data Warehouse beräknings resurser skalas upp eller ned eller stängas av helt om inga aktiva processer kräver beräknings resurser.

En SQL Server belastningsutjämnare hanterar SQL-trafik för att säkerställa hög prestanda. Alla virtuella datorer i den här referens arkitekturen distribueras i en tillgänglighets uppsättning med SQL Server instanser som kon figurer ATS i en tillgänglighets grupp som alltid är tillgängliga. Den här konfigurationen ger möjligheter till hög tillgänglighet och katastrof återställning.

Denna data lager referens arkitektur innehåller också en Active Directory nivå för hantering av resurser i arkitekturen. Active Directory under nätet gör det enkelt att införa en större Active Directory skogs struktur. På så sätt kan miljön köras kontinuerligt, även om åtkomst till den större skogen inte är tillgänglig. Alla virtuella datorer är domänanslutna till Active Directory nivån. De använder Active Directory grup principer för att tillämpa konfigurationer för säkerhet och efterlevnad på operativ system nivå.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda data Center för återhämtning. Geo-redundant lagring garanterar att data replikeras till ett sekundärt Data Center hundratals mil bort och att de lagras som tre kopior i data centret. Den här överenskommelsen förhindrar en negativ händelse på kundens primära Data Center från att resultera i förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory (RBAC-baserad åtkomst kontroll i Azure AD) används för att styra åtkomsten till distribuerade resurser. Dessa resurser innehåller kund nycklar i Azure Key Vault. System hälsan övervakas via Azure Security Center och Azure Monitor. Kunderna konfigurerar båda övervaknings tjänsterna för att avbilda loggar. System hälsan visas på en enda instrument panel som är lätt att använda.

En virtuell dator fungerar som en hanterings skydds-värd. Det ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data läses in i mellanlagringsområdet via den här hanterings skydds-värden. *Vi rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.*

![Data lager för NIST SP 800-171-referens arkitektur diagram](images/nist171-dw-architecture.png "Data lager för NIST SP 800-171-referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Tillgänglighetsuppsättningar
    - Active Directory domänkontrollanter
    - SQL Server klusternoder och vittne
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor (loggar)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure virtuella maskiner
    - (1) skydds-värd
    - (2) Active Directory domänkontrollant
    - (2) SQL Server klusternod
    - (1) SQL Server vittne
- Azure Virtual Network
    - (1)/16 nätverk
    - (4)/24 nätverk
    - (4) nätverks säkerhets grupper
- Recovery Services-valv
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informations lager för företag som drar nytta av massivt parallell bearbetning för att snabbt köra komplexa frågor över petabyte av data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera Big data till SQL Data Warehouse och använda kraften i massivt parallell bearbetning för att köra analys med höga prestanda.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabb generering av rapporter med tabeller, diagram, kartor, mätare, matriser och mycket mer för SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) gör det enkelt att identifiera och förstå data källor för de användare som hanterar data. Vanliga data källor kan registreras, taggas och sökas efter data. Data behålls på den befintliga platsen, men en kopia av dess metadata läggs till Data Catalog. En referens till data käll platsen ingår. Metadata indexeras för att göra varje data källa lätt att upptäcka via sökning. Indexeringen gör det också lättare för användare som identifierar dem.

**Skydds-värd**: Skydds-värden är den enda post adressen som användarna kan använda för att få åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure-diagnostik-tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av VM-resurser när de inte används.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) är aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs.

### <a name="virtual-network"></a>Virtuellt nätverk
Den här referens arkitekturen definierar ett privat virtuellt nätverk med adress utrymmet 10.0.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSG: er) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande NSG: er finns:
  - En NSG för data skiktet (SQL Server kluster, SQL Server vittne och SQL Load Balancer)
  - En NSG för hanterings skyddss värden
  - En NSG för Active Directory
  - En NSG för SQL Server Reporting Services

Varje NSG: er har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto.
  - Azure Monitor loggar är anslutna till [NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)-diagnostiken.

**Undernät**: Varje undernät är associerat med motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom flera mått. Dessa åtgärder omfattar kryptering och databas granskning.

**Azure Storage**: För att uppfylla kraven för krypterade data i vila använder [](https://azure.microsoft.com/services/storage/) all lagring [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Den här funktionen hjälper till att skydda och skydda data som stöd för organisatoriska säkerhets åtaganden och krav på efterlevnad.

**Azure Disk Encryption**: [Disk kryptering](../azure-security-disk-encryption-overview.md) använder BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen integreras med Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database granskning](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Den utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar. Den innehåller säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som oformaterad text i databas systemet. När data kryptering har Aktiver ATS kan endast klient program eller App-servrar med åtkomst till nycklarna komma åt oformaterad text data.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av data ämnen. Användare kan lägga till anpassade egenskaper till databas objekt. De kan också tagga data som "utgångna" för att stödja program logik för att förhindra bearbetning av associerade finansiella data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) gör det möjligt för användare att definiera principer för att begränsa åtkomsten till data för att avbryta bearbetningen.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till användare eller program utan privilegier. Den kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Dynamisk data maskning hjälper till att minska åtkomsten så att känsliga data inte avslutar databasen via obehörig åtkomst. *Kunderna ansvarar för att justera inställningarna för att följa deras databas schema.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure AD och innehåller de användare som har åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i så här integrerar du [program med Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Databasens kolumn kryptering använder också Azure AD för att autentisera programmet för att SQL Database. Mer information finns i så här [skyddar du känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan användas av administratörer för att definiera detaljerade åtkomst behörigheter. Med den kan de endast bevilja den åtkomst mängd som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad åtkomst till Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till resurser och data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kan användas av kunder för att minimera antalet användare som har åtkomst till viss information, till exempel data. Administratörer kan använda Azure AD Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter. Den konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter. Den undersöker också misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och-tjänster. Följande Key Vault funktioner hjälper kunder att skydda data:
- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en maskin varu säkerhetsmodul – skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med RBAC.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för virtuella datorer tillhandahåller real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Kunder kan konfigurera aviseringar som genererar när kända skadlig eller oönskad program vara försöker installeras eller köras på skyddade virtuella datorer.

**Azure Security Center**: Med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Security Center också åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller en misstänkt aktivitet äger rum. Kunder kan använda [anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) för att definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Security Center innehåller prioriterade säkerhets aviseringar och incidenter. Security Center gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot. Incident hanterings team kan använda rapporterna när de undersöker och reparerar hot.

Den här referens arkitekturen använder även funktionen för [sårbarhets bedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Security Center. När den har kon figurer ATS rapporterar en partner agent (till exempel Qualys) sårbarhets data till partnerns hanterings plattform. Partnerns hanteringsplattform skickar i sin tur tillbaks sårbarhets- och hälsoövervakningsdata till Security Center. Kunder kan använda den här informationen för att snabbt identifiera sårbara virtuella datorer.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server arbets belastningarna grupperas i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

**Recovery Services valv**: [Recovery Services valvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av virtuella datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn. Den här processen påskyndar återställnings tider.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, lagrings loggar, Key Vault gransknings loggar och Azure Application Gateway-åtkomst och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar, för att uppfylla de särskilda kraven.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in organiseras de i separata tabeller för varje datatyp inom Log Analytics arbets ytor. På så sätt kan alla data analyseras tillsammans, oavsett den ursprungliga källan. Security Center integreras med Azure Monitor-loggar. Kunder kan använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory utvärdering](../../azure-monitor/insights/ad-assessment.md): Active Directory hälso kontrolls lösningen utvärderar hälso-och hälso tillståndet för Server miljöer med jämna mellanrum. Den innehåller en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum. Den ger kunder en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution. Den rapporterar även hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från SQL Database. Kunder kan använda Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen för att enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Övervakaren](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/nist171-dw-tm) eller finns här. Den här modellen kan hjälpa kunderna att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![Data lager för NIST SP 800-171-hot modell](images/nist171-dw-threat-model.png "Data lager för NIST SP 800-171-hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – NIST SP 800-171-kund ansvar](https://aka.ms/nist171-crm) visas alla säkerhets kontroller som krävs av NIST SP 800-171. Den här matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-NIST SP 800-171-kontroll för data lager kontroll](https://aka.ms/nist171-dw-cim) innehåller information om vilka NIST SP 800-171-kontroller som omfattas av data lager arkitekturen. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribueras som en del av denna data lager referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk. Den här anslutningen äger rum via Internet. Kunder kan använda den här anslutningen för att på ett säkert sätt ange "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft ännu fler säkra anslutnings alternativ. ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. ExpressRoute-anslutningar ansluter direkt till kundens teleoperatörs leverantör. Det innebär att data inte färdas över Internet och inte exponeras för den. Dessa anslutningar ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extrahera-transformering-load process
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i SQL Data Warehouse utan att behöva ett separat ETL-eller import verktyg. PolyBase ger åtkomst till data via T-SQL-frågor. Microsoft Business Intelligence-och Analysis-stacken och verktyg från tredje part som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-ad-setup"></a>Installation av Azure AD
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. Lokala Active Directory kan integreras med Azure AD i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md). Kunder kan också knyta samman den distribuerade Active Directory infrastrukturen (domänkontrollanter) till Azure AD. Det gör du genom att göra den distribuerade Active Directory-infrastrukturen till en under domän till en Azure AD-skog.

### <a name="optional-services"></a>Valfria tjänster
Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagring av formaterade och oformaterade data. Följande tjänster kan läggas till i den här referens arkitekturen baserat på kund krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad moln tjänst som är byggd för komplexa hybrid ETL-, extraherings-och data integrerings projekt. Data Factory har funktioner som hjälper dig att spåra och hitta data. Verktyg för visualisering och övervakning identifierar när data anlänt och var de kom från. Kunder kan skapa och schemalägga data drivna arbets flöden, som kallas pipelines, som matar in data från olika data lager. De kan använda pipelines för att mata in data från interna och externa källor. Kunderna kan sedan bearbeta och transformera data för utdata till data lager, t. ex. SQL Data Warehouse.
- Kunder kan mellanlagra ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) för att samla in data om storlek, typ och inmatnings hastighet på en plats för drift och analys av analys. Azure Data Lake har funktioner som möjliggör extrahering och konvertering av data. Data Lake Store är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet. Den integreras också snyggt med andra Azure-tjänster, t. ex. SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
