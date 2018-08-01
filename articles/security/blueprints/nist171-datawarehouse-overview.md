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
ms.openlocfilehash: e2d33624e7742986f11129ff4d719edb944ebab1
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392091"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - datalagret för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera ett data warehouse-arkitektur i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att skapa och konfigurera sina egna data warehouse-lösningar i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och bör inte användas som-är i en produktionsmiljö. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen tillhandahåller en referensarkitektur som implementerar ett informationslager i molnet med höga prestanda och säker. Det finns två separata datanivåer i den här arkitekturen: en där data har importerats, lagras och mellanlagras i en klustrad SQL-miljö och en annan för Azure SQL Data Warehouse där data har lästs in med ett verktyg för extrahering, transformering och laddning (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan köra analyser i massiv skala.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen inkluderar SQL Server Reporting Services för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Azure SQL Data Warehouse lagrar data i Relationstabeller med kolumnbaserad lagring, ett format som avsevärt minskar lagringskostnaderna för data samtidigt förbättra frågeprestanda. Beroende på användningskrav beräkningsresurser i Azure SQL Data Warehouse skalas upp eller ned eller stänga av helt om det finns inga aktiva processer som kräver beräkningsresurser.

SQL-belastningsutjämnare hanterar SQL-trafik, att säkerställa hög prestanda. Alla virtuella datorer i den här referensarkitekturen distribuerar i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en Always On-tillgänglighetsgrupp för funktioner för hög tillgänglighet och katastrofåterställning.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större Active Directory-skog struktur, så att för kontinuerlig drift av miljön även om åtkomst till större skogen är inte tillgänglig. Alla virtuella datorer är domänansluten till Active Directory-nivå och använda grupprinciper för Active Directory för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda datacenter för återhämtning. Geografiskt redundant lagring garanterar att data replikeras till ett sekundärt datacenter hundratals mil bort och igen lagras som tre kopior inom det datacenter som förhindrar att en incident på kundens primära datacenter vilket leder till förlust av data.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.

En virtuell dator fungerar som en management skyddsmiljö-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data har lästs in i mellanlagringsområdet via den här management skyddsmiljö-värd. **Microsoft rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![Datalager för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-dw-architecture.png "datalager för SP NIST 800-171 referens för Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Tillgänglighetsuppsättningar
    - Active Directory-domänkontrollanter
    - SQL-klusternoder och vittne
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
    - (1) /16 nätverk
    - (4) /24 nätverk
    - (4) nätverkssäkerhetsgrupper
- Recovery Services-valv
- SQL Data Warehouse
- SQLServer Reporting Services

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informationslager för företag som utnyttjar massivt parallell bearbetning för att snabbt köra komplexa frågor över petabyte med data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera stordata till SQL Data Warehouse och utnyttja kraften hos massivt parallell bearbetning för att köra högpresterande analyser.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer för Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör datakällor enkelt identifiera och förstå hur av de användare som hanterar data. Gemensamma datakällor kan registrerats, taggade och söka efter data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. Nätverkssäkerhetsgrupper kan användas för att skydda trafik på ett undernät eller en enskild virtuell dator-nivå. Följande nätverkssäkerhetsgrupper finns:
  - En nätverkssäkerhetsgrupp för datanivå (SQL Server-kluster, vittne för SQL Server och SQL-belastningsutjämnare)
  - En nätverkssäkerhetsgrupp för hantering av skyddsmiljö-värd
  - En nätverkssäkerhetsgrupp för Active Directory
  - En nätverkssäkerhetsgrupp för SQL Server Reporting Services

Var och en av nätverkssäkerhetsgrupperna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje grupp:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Log Analytics är ansluten till den [network security group diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande grupp.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att tillhandahålla volymkryptering för operativsystem och datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL Database-granskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt data i klartext.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av registrerade, eftersom den låter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogik för att förhindra att bearbetningen av som är associerade finansiella data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med att minska åtkomst till känsliga data så att det inte finns databasen via obehörig åtkomst. **Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure Active Directory. Läs mer om hur du [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad åtkomst för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt resurser och data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information, till exempel data.  Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvarusäkerhetsmodul skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Azure Security Center**: med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

Dessutom är den här referensarkitekturen använder den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Azure Security Center. När du konfigurerat rapporterar en partner-agenten (t.ex. Qualys) sårbarhetsdata till partnerns hanteringsplattform. I sin tur innehåller partnerns hanteringsplattform säkerhetsproblem och hälsoövervakningsdata tillbaka till Azure Security Center, så att kunder kan snabbt identifiera sårbara virtuella datorer.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, att aktivera återställningen går snabbare.

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

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-dw-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![Datalager för SP NIST 800-171 hotmodell](images/nist171-dw-threat-model.png "datalager för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen – SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 Data Warehouse kontroll implementering matrisen](https://aka.ms/nist171-dw-cim) innehåller information som på vilka NIST SP 800-171-kontroller omfattas av data warehouse-arkitektur, inklusive detaljerad beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Process för extrahering, transformering och laddning
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Data Warehouse utan att behöva en separat extrahering, transformering och laddning eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsofts business intelligence och analys stack, samt från tredje part-verktyg som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig Azure Active Directory genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en Azure Active Directory-skog.

### <a name="optional-services"></a>Valfria tjänster
Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagra data formaterade och oformaterad. Följande tjänster kan läggas till i denna Referensarkitektur beroende på kundernas krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid extrahering, transformering och laddning, extract-load-transform och dataintegreringsprojekt. Azure Data Factory har funktioner för att spåra och hitta data, inklusive visualisering och övervakningsverktyg för att identifiera när data har kommit fram och där de kommer ifrån. Med Azure Data Factory kan kunder skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Dessa pipelines gör att kunderna kan hämta data från både interna och externa källor. Kunder kan bearbeta och omvandla data för utdata till datalager som Azure SQL Data Warehouse.
- Kunder kan mellanlagra Ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), vilket gör att infångandet av data med valfri storlek, typ, och inmatning öka hastigheten på en enda plats för driftsanalyser och undersökande analyser.  Azure Data Lake har funktioner som gör att extrahera och konvertering av data. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
