---
title: Azure-säkerhet och efterlevnad skissen - datalagret för Storbritannien NHS
description: Azure-säkerhet och efterlevnad skissen - datalagret för Storbritannien NHS
services: security
author: jomolesk
ms.assetid: f4e4b939-88db-4d78-8fa9-c2a12b2c025b
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 12042b853682efcff2de285a97508b8a81b1d647
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610052"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-uk-nhs"></a>Azure Blueprint för säkerhet och efterlevnad: Data Warehouse för Storbritannien NHS

## <a name="overview"></a>Översikt

Den här Azure Blueprint för säkerhet och efterlevnad tillhandahåller en Referensarkitektur och vägledning för en informationslagerlösning som passar på ett säkert sätt mata in, mellanlagring, lagra och interagera med känsliga sjukvårdsuppgifter. Den här lösningen demonstreras sätt där kunder kan följa riktlinjerna i den [Cloud Security bra praxis](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) har publicerats av [NHS Digital](https://digital.nhs.uk/), en partner till Storbritannien (UK) Department of Health och sociala försiktighet (DHSC). Bra tips molnet säkerhetsguiden baseras på 14 [Molnsäkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) publiceras genom den Storbritannien nationella Cyberhot Security Center (NCSC).

Den här referensarkitekturen och Implementeringsguide för hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö utan ytterligare konfiguration. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen tillhandahåller en referensarkitektur som implementerar ett informationslager i molnet med höga prestanda och säker. Det finns två separata datanivåer i den här arkitekturen: en där data importeras, lagras och mellanlagras i en klustrad SQL-miljö, och en annan för Azure SQL Data Warehouse där data läses med hjälp av en extrahera, transformera läsa in verktyget (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan köra analyser i massiv skala.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen inkluderar SQL Server Reporting Services för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Data i Azure SQL Data Warehouse lagras i Relationstabeller med kolumnbaserad lagring, ett format som avsevärt minskar lagringskostnaderna för data samtidigt förbättra frågeprestanda.  Beroende på användningskrav beräkningsresurser i Azure SQL Data Warehouse skalas upp eller ned eller stänga av helt om det finns inga aktiva processer som kräver beräkningsresurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, att säkerställa hög prestanda. Alla virtuella datorer i den här referensarkitekturen distribuerar i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en Always On-tillgänglighetsgrupp för funktioner för hög tillgänglighet och katastrofåterställning.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser och nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.


Denna Referensarkitektur för data warehouse innehåller även en Active Directory-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större Active Directory-skog struktur, så att för kontinuerlig drift av miljön även om åtkomst till större skogen är inte tillgänglig. Alla virtuella datorer är domänansluten till Active Directory-nivå och använda grupprinciper för Active Directory för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

En virtuell dator fungerar som en management skyddsmiljö-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data har lästs in i mellanlagringsområdet via den här management skyddsmiljö-värd. **Microsoft rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![Data Warehouse för Storbritannien NHS referens för Arkitekturdiagram](images/uknhs-datawarehouse-architecture.png?raw=true "Data Warehouse för Arkitekturdiagram för Storbritannien NHS-referens")

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

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
- Azure Automation
- Azure Virtual Machines
    - (1) skyddsmiljö-värd
    - (2) active Directory-domänkontrollant
    - (2) SQL Server-klusternod
    - (1) SQL Server-vittne
- Azure Virtual Network
    - (1) /16 nätverk
    - (4) /24 nätverk
    - (4) nätverkssäkerhetsgrupper
- Recovery Services Vault
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Distributionsarkitektur för

Följande avsnitt beskriver elementen distribution och implementering.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informationslager för företag som utnyttjar massivt parallell bearbetning för att snabbt köra komplexa frågor över petabyte med data, så att användarna kan effektivt identifiera sjukvårdsuppgifter. Användare kan använda enkla PolyBase T-SQL-frågor för att importera stordata till SQL Data Warehouse och utnyttja kraften hos massivt parallell bearbetning för att köra högpresterande analyser.

**SQLServer Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer för Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör datakällor enkelt identifiera och förstå hur av de användare som hanterar data. Gemensamma datakällor kan registrerats, taggade och söks igenom efter hälsotillstånd-relaterade data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

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
- Azure Monitor-loggar är ansluten till den [nätverkssäkerhetsgrupp&#39;s diagnostikloggar](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerad med dess motsvarande grupp.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: Att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av NHS Digital.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:

- [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
- [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
- Azure SQL-databasen är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
- [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
- [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper till att identifiera och minska åtkomst till data så att det inte finns databasen via obehörig åtkomst. Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsoft&#39;s flera innehavare molnbaserad katalog- och identity management-tjänsten. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till Azure SQL-databasen.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad behörighet för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisation&#39;s identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisation&#39;s identiteter , och undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet

**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda och komma åt dessa data:

- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvarusäkerhetsmodul skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

Dessutom är den här referensarkitekturen använder den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Azure Security Center. När du konfigurerat rapporterar en partner-agenten (t.ex. Qualys) sårbarhetsdata till partnerns hanteringsplattform. I sin tur innehåller partnerns hanteringsplattform säkerhetsproblem och hälsoövervakningsdata tillbaka till Azure Security Center, så att kunder kan snabbt identifiera sårbara virtuella datorer.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av virtuella Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, att aktivera återställningen går snabbare.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Azure Monitor-loggar**: Dessa loggar konsolideras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp, som låter alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Azure Monitor-loggar så att kunder kan använda Kusto-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Azure [övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Database. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/uknhs-dw-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![Data Warehouse för Storbritannien NHS hotmodell](images/uknhs-datawarehouse-threat-model.png?raw=true "Data Warehouse för Storbritannien NHS hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen – Storbritannien NHS kundens ansvar matrisen](https://aka.ms/uknhs-crm) visar en lista över alla Storbritannien NHS krav. Den här matrisen information om genomförandet av varje princip ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen – Storbritannien NHS Data Warehouse implementering matrisen](https://aka.ms/uknhs-dw-cim) innehåller information som på vilka Storbritannien NHS krav hanteras av data warehouse-arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad principen.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute

En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för PaaS web application. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt &quot;tunnel&quot; informationen i en krypterad anslutning mellan kunden&#39;s nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det här är en direkt anslutning av kunden&#39;s-providern telekommunikation data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Process för extrahering, transformering och laddning

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data till Azure SQL Data Warehouse utan att behöva en separat extrahera, transformera, läsa in eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsofts business intelligence och analys stack, samt från tredje part-verktyg som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig Azure Active Directory genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en Azure Active Directory-skog.

### <a name="optional-services"></a>Valfria tjänster

Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagra data formaterade och oformaterad. Följande tjänster kan läggas till i denna Referensarkitektur beroende på kundernas krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som skapats för komplexa hybrid extrahering, transformering och laddning och dataintegreringsprojekt. Azure Data Factory har funktioner för att spåra och hitta health-relaterad data, inklusive visualisering och övervakningsverktyg för att identifiera när data har kommit fram och där de kommer ifrån. Med Azure Data Factory kan kunder skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Dessa pipelines gör att kunderna kan hämta data från både interna och externa källor. Kunder kan bearbeta och omvandla data för utdata till datalager som Azure SQL Data Warehouse.
- Kunder kan mellanlagra Ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), vilket gör att infångandet av data med valfri storlek, typ, och inmatning öka hastigheten på en enda plats för driftsanalyser och undersökande analyser.  Azure Data Lake har funktioner som gör att extrahera och konvertering av data. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
