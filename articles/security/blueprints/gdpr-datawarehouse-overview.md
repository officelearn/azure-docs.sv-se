---
title: Azure-säkerhet och efterlevnad modell - datalagret för BNPR
description: Azure-säkerhet och efterlevnad modell - datalagret för BNPR
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure-säkerhet och efterlevnad utkast: datalagret för BNPR

## <a name="overview"></a>Översikt
Allmänna Data Protection förordning (BNPR) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga data överträdelser och utbilda sekretess personal och andra anställda. BNPR får enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera och analysera personliga data. BNPR inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU), eller att samla in och analysera data som är knutna till Europa boende. BNPR gäller oavsett var en organisation finns.

Microsoft har utvecklat Azure med branschledande säkerhetsåtgärder och sekretesspolicy skydda data i molnet, inklusive kategorier av personuppgifter som identifieras av BNPR. Microsofts [avtalsvillkor](http://aka.ms/Online-Services-Terms) genomför Microsoft kraven för processorer.

Den här Azure-säkerhet och efterlevnad utkast vägledning för att distribuera ett data warehouse-arkitektur i Azure som hjälper till med krav på BNPR. Den här lösningen visar sätt som kunder kan använda för att uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund att skapa och konfigurera sina egna data warehouse-lösningar i Azure-kunder. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i transporten BNPR kompatibiliteten:
1. Identifiera: Identifiera vilka personuppgifter finns och var den finns.
2. Hantera: Styr hur personuppgifter används och komma åt.
3. Skydda: Upprätta säkerhetsåtgärder för att förebygga, upptäcka och åtgärda säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och bryta mot meddelanden.

Den här referensen för arkitekturen, associerade Implementeringsguide och hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till deras specifika krav och ska inte användas som – i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som BNPR-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen ger en referensarkitektur som implementerar ett datalager för hög prestanda och säker moln. Det finns två separata datanivåer i den här arkitekturen: en där data som importeras lagras och mellanlagras i en klustrad SQL-miljö och en annan för Azure SQL Data Warehouse där data har lästs in med ett ETL-verktyg (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse, köra analytics i massiv skala.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen innehåller SQL Server Reporting Services (SSRS) för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafiken krypteras med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Data i Azure SQL Data Warehouse lagras i tabeller med kolumner lagring, ett format som avsevärt minskar lagringskostnaderna data samtidigt förbättra frågeprestanda.  Beroende på kraven för användning, Azure SQL Data Warehouse beräkningsresurser skalas upp eller ned eller stängas helt om det inte finns några aktiva processer som kräver beräkningsresurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, säkerställer hög prestanda. Alla virtuella datorer i denna Referensarkitektur distribuera i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet och katastrofåterställning funktioner.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory (AD)-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större AD-skog struktur, så att kontinuerligt i miljön, även när åtkomst till större skogen är tillgänglig. Alla virtuella datorer är domänanslutna Active Directory-nivån och använda grupprinciper för Active Directory för att tvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå.

En virtuell dator fungerar som en management skyddsmiljö värd, vilket ger en säker anslutning för administratörer att komma åt distribuerade resurser. Data läses in i mellanlagringsområdet via den här värden för hantering av skyddsmiljön. **Azure rekommenderar att konfigurera en VPN- eller Azure ExpressRoute-anslutning för hanterings- och import till referens arkitektur undernät.**

![Datalagret för BNPR referens Arkitekturdiagram](images/gdpr-datawarehouse-architecture.png?raw=true "datalagret för BNPR referens Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

-   Azure Virtual Machines
    - (1) skyddsmiljö värden
    -   (2) active Directory-domänkontrollant
    -   (2) SQL Server-klusternod
    -   (1) SQL Server-vittne
-   Tillgänglighetsuppsättningar
    - Active Directory-domänkontrollanter
    - SQL-klusternoder och vittne
-   Virtual Network
    - (4) undernät
    -   (4) Nätverkssäkerhetsgrupper
- SQL Data Warehouse
- SQLServer Reporting Services
- SQL Azure belastningsutjämnare
- Azure Active Directory
- Recovery Services-valvet
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Arkitektur för distribution
I följande avsnitt beskrivs distribution och implementeringen elementen.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är en Enterprise Data Warehouse (EDW) som utnyttjar massivt parallell bearbetning (MPP) för att köra komplexa frågor snabbt över petabyte data, att tillåta användare att identifiera effektivt personliga data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera ett stort data till SQL Data Warehouse och utnyttja kraften i MPP för att köra högpresterande analytics.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) innehåller snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser, och mer för Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör enkelt identifiera och förstå datakällor för användare som hanterar data. Gemensamma datakällor kan registrerats, märkta och söka efter personlig data. Dina data blir kvar i den befintliga platsen, men en kopia av dess metadata har lagts till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

**Skyddsmiljö värden**: skyddsmiljö värden är den enda post som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö värden ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärråtkomst trafiken från offentliga IP-adresser på listan över säkra. Källan för trafiken måste definieras i den Nätverkssäkerhetsgrupp (NSG) för att tillåta fjärråtkomst fjärrskrivbord (RDP)-trafik.

Den här lösningen skapar en virtuell dator som värd domänanslutna skyddsmiljö med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägget](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med hjälp av Azure Key Vault
-   En [automatisk avstängning princip](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender autentiseringsuppgifter Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverats så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från operativsystem

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme för 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor (ACL) som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller individuella VM-nivå. Det finns följande NSG: er:
  - En NSG för datanivå (SQL Server-kluster, vittne för SQL Server och SQL-belastningsutjämnare)
  - En NSG för hantering av skyddsmiljön värden
  - En NSG för Active Directory
  - En NSG för SQLServer Reporting Services

Var och en av de NSG: er har specifika portar och protokoll öppnas så att lösningen fungerar korrekt och på ett säkert sätt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) aktiveras och lagras i ett lagringskonto
  - OMS Log Analytics är ansluten till den [NSGS diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom flera åtgärder, inklusive kryptering och granskning för databasen.

**Azure Storage**: att uppfylla krypterade data på övriga krav, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda och skydda personuppgifter för att stödja organisationens säkerhetsåtaganden och efterföljandekrav som definieras av BNPR.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för Operativsystemet och datadiskarna. Lösningen integreras med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

**Azure SQL Database**: I Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger försäkran som lagras i personliga data inte har omfattas obehörig åtkomst.
-   [Regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Alltid krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känslig personlig information aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering endast klientprogram eller appservrar med tillgång till nycklarna kan komma åt oformaterad text data.
- Den [utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funktionen kan användas för att avbryta bearbetningen av registrerade, som tillåter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att den bearbetning av associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att avbryta bearbetningen.
- [SQL Database dynamiska Data maskering (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att kombinera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och föreslår lämpliga masker som ska användas. Detta hjälper med identifiering av personliga uppgifter som är kvalificerade för BNPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder behöver justera DDM för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker innehåller funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till SQL-databasen.
-   Tillämpningen utförs autentiseringen med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skydda känsliga data i SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter att bevilja endast åtkomstnivå som användare måste utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer endast vissa åtgärder för att komma åt personliga data. Prenumerationen åtkomst begränsas till administratör för prenumerationen.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har tillgång till viss information, till exempel personuppgifter.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att tillämpa på begäran, just-in-time administrativ åtkomst vid behov.
- [AAD identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder kan lösas.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälpa kunderna skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på grundval av behov.
- Key Vault åtkomstprinciper definieras med minsta behörighet till nycklar och hemligheter.
- Alla nycklar och hemligheter i Nyckelvalvet ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade av särskilda maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med RBAC.
- Diagnostik-loggarna för Key Vault aktiveras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklarna är begränsade till de som krävs.

**Hantering av korrigering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten via vilken uppdaterade distributioner kan skapas för korrigering virtuella datorer när de behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installeras eller köras på den skyddade virtuella datorer.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom öppnar Azure Security Center befintliga konfigurationen av Azure-tjänster att ge konfiguration och tjänsterekommendationer för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) för alla upptäckta hot mot hjälpa incidenter team undersöka och åtgärda hot.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: serverarbetsbelastningar grupperas i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% SLA för Azure.

**Recovery Services-ventilen**: den [Återställningstjänstvalvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) inrymmer säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i Azure i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, aktivera snabbare återställning.

### <a name="logging-and-auditing"></a>Granskning och loggning
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att bestämma en åtgärd initieraren tid på förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och loggar för kön.
- **Logga arkivering**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar till bevarande organisation-specifika krav. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom ingår följande OMS-lösningar som en del av denna arkitektur:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/gdprDWdfd) eller finns nedan. Den här modellen hjälper kunder att förstå punkterna i risken i infrastrukturen för system när ändringar görs.

![Datalagret för BNPR hotmodell](images/gdpr-datawarehouse-threat-model.png?raw=true "datalagret för BNPR hotmodell")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad
Den [Azure-säkerhet och efterlevnad utkast – BNPR kunden ansvar matrisen](https://aka.ms/gdprCRM) visar domänkontrollant och processor ansvar för alla BNPR artiklar. Observera att för Azure-tjänster, en kund är vanligen kontrollanten och Microsoft fungerar som processorn.

Den [Azure-säkerhet och efterlevnad modell - BNPR Data Warehouse implementering matrisen](https://aka.ms/gdprDW) innehåller information om vilka BNPR artiklar med hjälp av data warehouse-arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN- och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. Kunder kan genom på lämpligt sätt att skapa en VPN eller ExpressRoute, lägga till ett lager av dataskydd under överföring.

Genom att implementera en säker VPN-tunnel med Azure, kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure Virtual Network. Den här anslutningen sker via Internet och ger kunder möjlighet att på ett säkert sätt ”tunnel” information i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Den [IPSec-tunnelläge](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism för.

Eftersom trafiken i VPN-tunnel passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutning. Azure ExpressRoute är en fast WAN länken mellan Azure och en lokal plats eller en värdleverantör för Exchange. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Dessutom eftersom det här är en direkt anslutning av kundens telekommunikation providern data överföras inte via Internet och därför visas inte för den.

Bästa praxis för att implementera en säker hybrid-nätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Extrahera transformeringen inläsning (ETL) process
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data till Azure SQL Data Warehouse utan att behöva en separat ETL eller importera verktyget. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts affärsinformation och analys stacken, samt fristående verktyg som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Installationsprogram för Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är mycket viktigt att hantera distributionen och etablerar åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till ett befintligt AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="optional-services"></a>Valfria tjänster
Azure erbjuder en mängd olika tjänster för att hjälpa till med lagring och mellanlagring av formaterad och oformaterade data. Följande tjänster kan läggas till denna Referensarkitektur beroende på kundens krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid extrahera transform-inläsning (ETL) extrahera belastningen transformeringen ELT () och data integration projekt. Azure Data Factory har funktioner för att spåra och hitta dina personliga data, inklusive visualisering och övervakningsverktyg för att identifiera när data anlänt och var den kom från. Med Azure Data Factory kan kunderna skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Dessa pipelines kan kunder att mata in data från både interna och externa källor. Kunder kan sedan bearbeta och transformera data för utdata till datalager, till exempel Azure SQL Data Warehouse.
- Kunder kan mellanlagra Ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), vilket gör att insamlingen av data i alla storlekar, typ och införandet snabbare i en enda plats för drifts- och undersökande analyser.  Azure Data Lake har funktioner som möjliggör extrahering och konvertering av data. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster, till exempel Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
