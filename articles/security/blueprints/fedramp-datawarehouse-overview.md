---
title: Handlingsplan för säkerhet och efterlevnad i Azure-informations lager för automatisering av FedRAMP
description: Handlingsplan för säkerhet och efterlevnad i Azure-informations lager för automatisering av FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 79ed2b6e5d7bb600a79e12d19268035491f3fe08
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946868"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): Informations lager för automatisering av FedRAMP

## <a name="overview"></a>Översikt

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) är ett USA myndighets omfattande program som ger en standardiserad metod för säkerhetsbedömning, auktorisering och kontinuerlig övervakning av moln produkter och tjänster. Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för hur du levererar en Microsoft Azure-arkitektur för data lager som hjälper till att implementera en delmängd av FedRAMP High Controls. Den här lösningen ger vägledning om distribution och konfiguration av Azure-resurser för en gemensam referens arkitektur som demonstrerar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad och fungerar som en grund för kunderna att Bygg och konfigurera egna data lager lösningar i Azure.

Den här referens arkitekturen, de tillhör ande guiderna för kontroll implementering och hot modeller är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav och bör inte användas i en produktions miljö. Det räcker inte att distribuera ett program i den här miljön utan att ändra kraven i FedRAMP hög bas linje. Observera följande:
- Arkitekturen ger till gång till en bas linje som hjälper kunder att distribuera arbets belastningar till Azure på ett FedRAMP-kompatibelt sätt.
- Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här lösningen tillhandahåller en referens arkitektur för data lager som implementerar ett informations lager med hög prestanda och säkra moln. Det finns två separata data nivåer i den här arkitekturen: en där data importeras, lagras och mellanlagras i en klustrad SQL-miljö och en annan för den Azure SQL Data Warehouse där data läses in med ett ETL-verktyg (t. [](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) ex. PolyBase T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan analyser köras i en enorm skala.

Microsoft Azure erbjuder en rad olika rapporterings-och analys tjänster för kunden. Den här lösningen innehåller SQL Server Reporting Services (SSRS) för snabb skapandet av rapporter från Azure SQL Data Warehouse. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

Data i Azure SQL Data Warehouse lagras i Relations tabeller med lagring i kolumner, ett format som avsevärt minskar kostnaderna för data lagring samtidigt som du förbättrar prestandan för frågor.  Beroende på användnings kraven kan Azure SQL Data Warehouse beräknings resurser skalas upp eller ned eller stängas av helt om det inte finns några aktiva processer som kräver beräknings resurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, vilket säkerställer höga prestanda. Alla virtuella datorer i den här referens arkitekturen distribueras i en tillgänglighets uppsättning med SQL Server instanser som kon figurer ATS i en AlwaysOn-tillgänglighetsgruppen för funktioner med hög tillgänglighet och katastrof återställning.

Denna data lager referens arkitektur innehåller också en Active Directory (AD) för hantering av resurser i arkitekturen. Active Directory under nätet gör det enkelt att införa en större AD-skogs struktur, vilket möjliggör kontinuerlig drift av miljön även när åtkomst till den större skogen inte är tillgänglig. Alla virtuella datorer är domänanslutna till Active Directory nivån och använder Active Directory grup principer för att tillämpa konfigurationer för säkerhet och efterlevnad på operativ system nivå.

En virtuell dator fungerar som en hanterings skydds-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data läses in i mellanlagringsområdet via den här hanterings skydds-värden. **Azure rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.**

![Data lager för FedRAMP referens arkitektur diagram](images/fedramp-datawarehouse-architecture.png?raw=true "Data lager för FedRAMP referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

Azure virtuella maskiner
-   (1) skydds-värd
-   (2) Active Directory domänkontrollant
-   (2) SQL Server klusternod
-   (1) SQL Server vittne

Tillgänglighetsuppsättningar
-   (1) Active Directory domänkontrollanter
-   (1) SQL-klusternoder och vittne

Virtuellt nätverk
-   (4) undernät
-   (4) nätverks säkerhets grupper

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL-Load Balancer

Azure Active Directory

Recovery Services valv

Azure Key Vault

Azure Monitor-loggar

## <a name="deployment-architecture"></a>Distributions arkitektur

I följande avsnitt beskrivs utvecklings-och implementerings elementen.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informations lager för företag (analyslösningar) som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte data. Importera Big data till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och Använd kraften i MPP för att köra analys med höga prestanda.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) gör det möjligt att snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mycket mer för Azure SQL Data Warehouse.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger användare åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärran sluten trafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen (NSG).

En virtuell dator skapades som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Monitor loggar tillägg](../../virtual-machines/windows/extensions-oms.md)
-   [Azure-diagnostik tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Azure Key Vault (respekterar Azure Government, PCI DSS, HIPAA och andra krav)
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av virtuella dator resurser när den inte används
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs

### <a name="virtual-network"></a>Virtuellt nätverk
Den här referens arkitekturen definierar ett privat virtuellt nätverk med adress utrymmet 10.0.0.0/16.

**Nätverks säkerhets grupper**: [NSG: er](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller Access Control listor (ACL: er) som tillåter eller nekar trafik i ett VNet. NSG: er kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande NSG: er finns:
  - En NSG för data skiktet (SQL Server kluster, SQL Server vittne och SQL Load Balancer)
  - En NSG för hanterings skyddss värden
  - En NSG för Active Directory
  - En NSG för SQL Server Reporting Services

Varje NSG: er har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
  - Azure Monitor loggar är anslutna till [NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) -diagnostiken

**Undernät**: Varje undernät är associerat med motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage** För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database** Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:
-   [Autentisering och auktorisering i AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   SQL Database har kon figurer ATS för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av data och loggfiler för att skydda information i vila. TDE garanterar att lagrade data inte har blivit föremål för obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller App-servrar med åtkomst till nycklarna komma åt oformaterade data.
-   [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter att referens arkitekturen har distribuerats. Kunderna måste justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server arbets belastningarna grupperas i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

**Recovery Services valv**: [Recovery Services Vault](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av Azure-Virtual Machines i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställnings tider.

### <a name="logging-and-audit"></a>Loggning och granskning
[Azure Monitor loggar](../azure-security-disk-encryption-overview.md) innehåller omfattande loggning av system-och användar aktiviteter, samt system hälsan. Lösningen [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) samlar in och analyserar data som genererats av resurser i Azure och lokala miljöer.
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar omfattar Windows händelse system loggar och Azure Blob Storage, tabeller och Queue-loggar.
- **Brand Väggs loggar**: Application Gateway innehåller fullständiga diagnostik-och åtkomst loggar. Brand Väggs loggar är tillgängliga för WAF-aktiverade Application Gateway-resurser.
- **Logg arkivering**: Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering med en definierad kvarhållningsperiod på 2 dagar. Dessa loggar ansluter till Azure Monitor loggar för bearbetning, lagring och instrument panels rapportering.

Dessutom ingår följande övervaknings lösningar som en del av den här arkitekturen:
-   [AD-utvärdering](../../azure-monitor/insights/ad-assessment.md): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Utvärdering av program mot skadlig kod](../../security-center/security-center-install-endpoint-protection.md): Lösningen mot skadlig kod rapporterar om skadlig kod, hot och skydds status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation lösning lagrar, kör och hanterar Runbooks.
-   [Säkerhet och granskning](../../security-center/security-center-intro.md): Säkerhet och granskning-instrumentpanelen ger bättre inblick i säkerhets läget för resurser genom att tillhandahålla mått på säkerhets domäner, viktiga problem, identifieringar, Hot information och vanliga säkerhets frågor.
-   [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Uppdateringshantering](../../automation/automation-update-management.md): Med den Uppdateringshantering lösningen kan kund hantering av säkerhets uppdateringar för operativ systemet, inklusive status för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Azure-aktivitets loggar](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](../../azure-monitor/platform/collect-activity-logs.md): Med Ändringsspårning-lösningen kan kunder enkelt identifiera ändringar i miljön.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för identitets hantering i Azure-miljön:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) kan vara Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare som har åtkomst till SQL Database.
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure AD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.
-   Med [Azure Role-baserade Access Control (RBAC)](../../role-based-access-control/role-assignments-portal.md) kan du prioriterad åtkomst hantering för Azure. Åtkomst till prenumerationen är begränsad till prenumerations administratören.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL Database finns i exempel på [demonstrations program för Contoso-lektioner](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för Virtual Machines ger real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara, med konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera eller kör på skyddade virtuella datorer.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="expressroute-and-vpn"></a>ExpressRoute och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribueras som en del av denna data lager referens arkitektur. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Genom att konfigurera ExpressRoute eller en VPN-anslutning kan kunder lägga till ett skydds lager för data under överföring.

### <a name="extract-transform-load-etl-process"></a>Process för extrahera-Transform-load (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Data Warehouse utan att behöva ett separat ETL-eller import verktyg. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts Business Intelligence-och analys stack, samt verktyg från andra tillverkare som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med AAD i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md). Kunder kan också knyta samman den distribuerade Active Directory infrastrukturen (domänkontrollanter) till en befintlig AAD genom att göra den distribuerade Active Directory-infrastrukturen till en under domän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
Även om denna informations lager arkitektur inte är avsedd för distribution till den [kommersiella Azure](https://azure.microsoft.com/overview/what-is-azure/) -miljön, kan liknande mål uppnås genom tjänsterna som beskrivs i denna referens arkitektur, samt ytterligare tjänster som är tillgängliga endast i den kommersiella Azure-miljön. Observera att Azure Commercial har en FedRAMP JAB P-ATO på nivån måttlig påverkan, vilket gör det möjligt för myndigheter och partner att distribuera känslig känslig information till molnet som utnyttjar Azures kommersiella miljö.

Azure Commercial erbjuder en mängd olika tjänster som hanterar formaterad och oformaterad data lagring och mellanlagring som ska användas i data lager, inklusive:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad moln tjänst som är byggd för komplex hybrid extrahering – Transformation-load (ETL), Extract-load-Transform (ELT) och data integrerings projekt. Med hjälp av Azure Data Factory kan kunder skapa och schemalägga data drivna arbets flöden som kallas pipelines som matar in data från olika data lager. Kunderna kan sedan bearbeta och transformera data för utdata till data lager, t. ex. Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) möjliggör insamling av data i valfri storlek, typ och inmatnings hastighet på en enda plats för operativa och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet och kan integreras snyggt med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet (DFD) för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/blueprintdwthreatmodel) eller kan hittas nedan:

![Data lager för FedRAMP hot modell](images/fedramp-datawarehouse-threat-model.png?raw=true "Data lager för FedRAMP hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – FedRAMP hög kund ansvar](https://aka.ms/blueprinthighcrm) visas alla säkerhets kontroller som krävs av den höga bas linjen för FedRAMP. På samma sätt visar [matrisen handlingsplan för säkerhet och efterlevnad i Azure – FedRAMP måttliga kund ansvar](https://aka.ms/blueprintcrmmod) alla säkerhets kontroller som krävs av den FedRAMP måttliga bas linjen. Båda dokumenten anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP för hög kontroll](https://aka.ms/blueprintdwcimhigh) och implementering av [handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP för måttlig kontroll](https://aka.ms/blueprintdwcimmod) innehåller information om vilka kontroller som omfattas av informations lager arkitekturen för varje FedRAMP-bas linje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
