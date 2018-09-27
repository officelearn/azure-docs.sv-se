---
title: Azure-säkerhet och efterlevnad skissen - datalagret för SP NIST 800-171
description: Azure-säkerhet och efterlevnad skissen - datalagret för SP NIST 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f774aed837fac1829413493c98b6df0e4ca30600
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393221"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - datalagret för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera ett data warehouse-arkitektur i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visas hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav. Det fungerar också som en grund för kunder att skapa och konfigurera sina egna data warehouse-lösningar i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav. De bör inte användas som-är i en produktionsmiljö. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen tillhandahåller en referensarkitektur som implementerar ett informationslager i molnet med höga prestanda och säker. Det finns två separata datanivåer i den här arkitekturen. En nivå där data har importerats, lagras, och mellanlagras i en klustrad SQL-miljö. En annan nivå är för SQL data warehouse. Med den här nivån kan data läses med hjälp av ett verktyg för extrahering, transformering och laddning (ETL) (till exempel [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-frågor) för bearbetning. När data lagras i SQL Data Warehouse kan köra analyser i massiv skala.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen inkluderar SQL Server Reporting Services för att snabbt skapa rapporter från SQL data warehouse. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar vi att använda en betrodd certifikatutfärdare för förbättrad säkerhet.

Azure SQL Data Warehouse lagrar data i Relationstabeller med kolumnbaserad lagring. Det här formatet minskar lagringskostnaderna för data avsevärt medan det förbättrar frågeprestanda. Beroende på användningskrav beräkningsresurser för SQL Data Warehouse skalas upp eller ned eller stänga av helt om inga aktiva processer kräver beräkningsresurser.

En belastningsutjämnare för SQL Server hanterar SQL-trafiken för att säkerställa hög prestanda. Alla virtuella datorer (VM) i den här referensarkitekturen distribuerar i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en Always On-tillgänglighetsgrupp. Den här konfigurationen tillhandahåller funktioner för hög tillgänglighet och katastrofåterställning.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större struktur för Active Directory-skog. På så sätt kan kan miljön köras kontinuerligt, även om åtkomst till större skogen är inte tillgänglig. Alla virtuella datorer är domänanslutna till Active Directory-nivå. De kan använda grupprinciper för Active Directory för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda datacentret för återhämtning. GEO-redundant lagring garanterar att data replikeras till ett sekundärt Datacenter hundratals mil bort och igen lagras som tre kopior inom datacentret. Det här förhindrar att en incident på kundens primära datacenter vilket resulterar i förlust av data.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory (Azure AD) rollbaserad åtkomstkontroll (RBAC) används för att styra åtkomst till distribuerade resurser. Dessa resurser inkluderar Kundnycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar. Platssystemets hälsostatus visas i en enda instrumentpanel som är lätt att använda.

En virtuell dator fungerar som en management skyddsmiljö-värd. Det ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data har lästs in i mellanlagringsområdet via den här management skyddsmiljö-värd. *Vi rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.*

![Datalager för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-dw-architecture.png "datalager för SP NIST 800-171 referens för Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Tillgänglighetsuppsättningar
    - Active Directory-domänkontrollanter
    - SQL Server-klusternoder och vittne
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Log Analytics
- Azure Virtual Machines
    - (1) skyddsmiljö-värd
    - (2) active Directory-domänkontrollant
    - (2) SQL Server-klusternod
    - (1) SQL Server-vittne
- Azure Virtual Network
    - ((1) /16 nätverk
    - (4) /24 nätverk
    - (4) nätverkssäkerhetsgrupper
- Recovery Services-valv
- SQL Data Warehouse
- SQLServer Reporting Services

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informationslager för företag som drar nytta av massivt parallell bearbetning för att snabbt köra komplexa frågor över petabyte med data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera stordata till SQL data warehouse och använder kraften i massivt parallell bearbetning för att köra högpresterande analyser.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer för SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör det enkelt att identifiera och förstå av de användare som hanterar data datakällor. Gemensamma datakällor kan registrerats, taggade och söka efter data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog. En referens till datakällans plats ingår. Metadata indexeras för att göra det lätt att identifiera via sökningsfunktionen varje datakälla. Indexering gör det också att förstå att användare som identifierar den.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som användarna kan använda för att få åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd är en säker anslutning till distribuerade resurser eftersom endast fjärrtrafik från offentliga IP-adresser på en säker lista. Om du vill tillåta trafik för fjärrskrivbordet, måste källan av trafiken definieras i nätverkssäkerhetsgruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägg för Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av Virtuella datorresurser som.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiveras så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerat från vilket operativsystem som körs.

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller individuella VM-nivå. Följande Nätverkssäkerhetsgrupper finns:
  - En Nätverkssäkerhetsgrupp för datanivå (SQL Server-kluster, vittne för SQL Server och SQL-belastningsutjämnare)
  - En Nätverkssäkerhetsgrupp för hantering av skyddsmiljö-värd
  - En NSG för Active Directory
  - En Nätverkssäkerhetsgrupp för SQLServer Reporting Services

Varje NSG: erna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto.
  - Log Analytics är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera mått. Dessa åtgärder omfattar kryptering och database-granskning.

**Azure Storage**: att uppfylla krav för krypterade data i vila, alla [Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Den här funktionen kan du skydda och skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav.

**Azure Disk Encryption**: [diskkryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) använder BitLocker-funktion i Windows för att kryptera volymer för operativsystem och datadiskar. Lösningen kan integreras med Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL Database-granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Det fungerar i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger assurance som lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker. Det ger säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När datakryptering är aktiverat, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt oformaterad text data.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av datasubjekt. Användare kan lägga till anpassade egenskaper databasobjekt. De kan också tagga data som ”utgått” för att stödja programlogik för att förhindra att bearbetningen av associerade finansiella data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till replikdata användare eller program. Det kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Dynamisk datamaskning hjälper till att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. *Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsoft multitenant molnbaserade företagskatalogen och identitetshanteringen hanteringstjänsten. Alla användare för den här lösningen skapas i Azure AD och innehåller de användare som har åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Läs mer om hur du [integrera program med Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kolumnen databaskryptering använder också Azure AD för att autentisera program till SQL-databas. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kan användas av administratörer för att definiera detaljerade behörigheter. Med den, kan de bevilja endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad åtkomst för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt resurser och data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) kan användas av kunder för att minimera antalet användare som har åtkomst till viss information, till exempel data. Administratörer kan använda Azure AD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella sårbarheter som påverkar organisationens identiteter. Konfigurerar den automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter. Det kan också undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault kan du skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvara security-modul-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Uppdateringshantering**: Windows-datorer som distribuerats som en del av denna Referensarkitektur är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Kunder kan konfigurera aviseringar som genereras när känd skadlig eller oönskad programvara försöker installeras eller köras på skyddade virtuella datorer.

**Azure Security Center**: med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Security Center har även åtkomst till befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Security Center använder en mängd funktioner och meddela kunder om eventuella attacker som är inriktade på sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller misstänkt aktivitet inträffar. Kunder kan använda [anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Security Center tillhandahåller rangordnade säkerhetsaviseringar och incidenter. Security Center gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot. Incidenter team kan använda rapporterna när de undersöka och åtgärda hot.

Den här referensarkitekturen använder också den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) funktioner i Security Center. När den har konfigurerats kan rapporterar en partner-agenten (till exempel Qualys) sårbarhetsdata till partnerns hanteringsplattform. Partnerns hanteringsplattform skickar i sin tur tillbaks sårbarhets- och hälsoövervakningsdata till Security Center. Kunder kan använda den här informationen för att snabbt identifiera sårbara VM: ar.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse som uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa en hel virtuell dator. Den här processen snabbare återställning gånger.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Storage-loggar, granskningsloggar för Key Vault och Azure Application Gateway-loggar för åtkomst och brandväggen. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar att uppfylla deras specifika krav.

**Log Analytics**: de här loggarna konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp i Operations Management Suite-arbetsytor. På så sätt kan analyseras alla data tillsammans oavsett originalkälla. Security Center integrerar med Log Analytics. Kunder kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory-lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Det ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Tjänsten ger kunderna med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning. Här rapporteras även hur många agenter är inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från SQL-databas. Kunder kan använda Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösning lätt kan identifiera ändringar i miljön.

**Azure Monitor**: [övervakaren](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-dw-tm) eller hittar du här. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i infrastrukturen för system när de gör ändringar.

![Datalager för SP NIST 800-171 hotmodell](images/nist171-dw-threat-model.png "datalager för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen – SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 Data Warehouse kontroll implementering matrisen](https://aka.ms/nist171-dw-cim) innehåller information som på vilka SP NIST 800-171-kontroller omfattas av data warehouse-arkitektur. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen sker över Internet. Kunder kan använda den här anslutningen till på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom VPN-tunneln trafik passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat anslutningsalternativ för ännu mer säker. ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. ExpressRoute-anslutningar ansluta direkt till kundens telekommunikation providern. Därför kan överföras inte via Internet data och visas inte. De här anslutningarna erbjuder mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Process för extrahering, transformering och laddning
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i SQL Data Warehouse utan att behöva en separat ETL eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsoft business intelligence och analys stack och verktyg från tredje part som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-ad-setup"></a>Konfigurationen av Azure AD
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. Lokala Active Directory kan integreras med Azure AD med [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till Azure AD. Gör detta genom att kontrollera distribuerade Active Directory-infrastruktur en underdomän till en Azure AD-skog.

### <a name="optional-services"></a>Valfria tjänster
Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagra data formaterade och oformaterad. Följande tjänster kan läggas till i denna Referensarkitektur baserat på kundernas krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid ETL, extract-load-transform och dataintegreringsprojekt. Data Factory har funktioner för att spåra och hitta data. Identifierar när data har kommit fram och där de kommer ifrån visualisering och övervakningsverktyg. Kunder kan skapa och schemalägga datadrivna arbetsflöden, kallas pipelines som mata in data från olika datalager. De kan använda pipelines för att hämta data från interna och externa källor. Kunder kan bearbeta och omvandla data för utdata till datalager som SQL Data Warehouse.
- Kunder kan mellanlagra Ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) samla in data av valfri storlek, typer och inmatningshastighet på en plats för driftsanalyser och undersökande analyser. Azure Data Lake har funktioner som gör att extrahera och konvertering av data. Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Den även snyggt integrerat med andra Azure-tjänster, till exempel SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
