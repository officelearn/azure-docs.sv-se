---
title: Azure-säkerhet och efterlevnad skissen - datalagret för GDPR
description: Azure-säkerhet och efterlevnad skissen - datalagret för GDPR
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: d1099b813e84cd4885b011dec205a1e631fc6599
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250721"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure-säkerhet och efterlevnad skiss: Data Warehouse för GDPR

## <a name="overview"></a>Översikt
Den allmänna Dataskyddsförordningen GDPR (Data Protection) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga dataöverträdelser och träna sekretess personal och andra anställda. GDPR ger enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera eller analysera personliga data. Dataskyddsförordningen inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU) eller att samla in och analysera data som EU invånare. Dataskyddsförordningen gäller oavsett var det finns en organisation.

Microsoft har utformat Azure med branschledande säkerhetsåtgärder och sekretesspolicyer för att skydda data i molnet, inklusive kategorierna av personuppgifter som identifieras av GDPR. Microsofts [avtalsvillkor](https://aka.ms/Online-Services-Terms) genomför Microsoft till kraven i processorer.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att distribuera ett data warehouse-arkitektur i Azure som hjälper till med krav för GDPR. Den här lösningen visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att skapa och konfigurera sina egna data warehouse-lösningar i Azure. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i sin resa till GDPR-efterlevnad:
1. Identifiera: Identifiera vilka personliga data finns och var den finns.
2. Hantera: Styr hur personlig data används och komma åt.
3. Skydda: Upprätta säkerhetskontroller för att förhindra, upptäcka och svara på säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och tränga in meddelanden.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett sätt som GDPR-kompatibel.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen tillhandahåller en referensarkitektur som implementerar ett informationslager i molnet med höga prestanda och säker. Det finns två separata datanivåer i den här arkitekturen: en där data har importerats, lagras och mellanlagras i en klustrad SQL-miljö och en annan för Azure SQL Data Warehouse där data läses med hjälp av ett ETL-verktyg (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan köra analyser i massiv skala.

Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen inkluderar SQL Server Reporting Services (SSRS) för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Data i Azure SQL Data Warehouse lagras i Relationstabeller med kolumnbaserad lagring, ett format som avsevärt minskar lagringskostnaderna för data samtidigt förbättra frågeprestanda.  Beroende på användningskrav beräkningsresurser i Azure SQL Data Warehouse skalas upp eller ned eller stänga av helt om det finns inga aktiva processer som kräver beräkningsresurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, att säkerställa hög prestanda. Alla virtuella datorer i den här referensarkitekturen distribuerar i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en AlwaysOn-tillgänglighetsgrupp för funktioner för hög tillgänglighet och katastrofåterställning.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory (AD)-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större AD-skog struktur, så att för kontinuerlig drift av miljön även om åtkomst till större skogen är inte tillgänglig. Alla virtuella datorer är domänansluten till Active Directory-nivå och använda grupprinciper för Active Directory för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

En virtuell dator fungerar som en management skyddsmiljö-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data har lästs in i mellanlagringsområdet via den här management skyddsmiljö-värd. **Azure rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![Data Warehouse för GDPR-referens för Arkitekturdiagram](images/gdpr-datawarehouse-architecture.png?raw=true "Data Warehouse för Arkitekturdiagram för GDPR-referens")

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

-   Azure Virtual Machines
    - (1) skyddsmiljö-värd
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
- Azure SQL-belastningsutjämnare
- Azure Active Directory
- Recovery Services-valv
- Azure Key Vault
- Log Analytics
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är en Enterprise Data mpp-modell som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte med data, så att användarna kan effektivt identifiera personliga data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera stordata till SQL Data Warehouse och utnyttja kraften i MPP för att köra högpresterande analyser.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer för Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) gör datakällor enkelt identifiera och förstå hur av de användare som hanterar data. Gemensamma datakällor kan registrerats, taggade och söks igenom efter personliga data. Dina data blir kvar i där, men en kopia av deras metadata läggs till i Data Catalog tillsammans med en referens till datakällans plats. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i den Nätverkssäkerhetsgrupp (NSG) för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Log Analytics-tillägget](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

### <a name="virtual-network"></a>Virtuellt nätverk
Denna Referensarkitektur definierar ett privat virtuellt nätverk med ett adressutrymme 10.0.0.0/16.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor (ACL) som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller individuella VM-nivå. Följande Nätverkssäkerhetsgrupper finns:
  - En Nätverkssäkerhetsgrupp för datanivå (SQL Server-kluster, vittne för SQL Server och SQL-belastningsutjämnare)
  - En Nätverkssäkerhetsgrupp för hantering av skyddsmiljö-värd
  - En NSG för Active Directory
  - En Nätverkssäkerhetsgrupp för SQLServer Reporting Services

Var och en av NSG: erna har specifika portar och protokoll som är öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Log Analytics är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda personuppgifter som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av GDPR.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger garantier att lagras personliga data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga personliga data aldrig ska visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt data i klartext.
- Den [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funktionen kan användas för att avbryta bearbetningen av registrerade, eftersom den låter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att den bearbetningen av associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att maskera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med identifiering av personliga data som är kvalificerade för GDPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder måste justerar DDM inställningarna för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till SQL-databasen.
-   Det utförs autentiseringen till programmet med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt personliga data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information, till exempel personuppgifter.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom kommer Azure Security Center åt befintliga konfiguration av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

### <a name="logging-and-auditing"></a>Loggning och granskning
[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) innehåller utförlig loggning av system- och användaraktivitet samt filsystemets hälsa. Den [Log Analytics](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och köloggar.
- **Logga arkivering**: alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning. Dessa loggar kan ansluta till Azure Log Analytics för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom ingår följande Log Analytics-lösningar som en del av den här arkitekturen:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): med Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): The säkerhet och granskning instrumentpanelen innehåller en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): uppdateringshanteringslösningen kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Change Tracking solution gör att kunder enkelt kan identifiera ändringar i miljön.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/gdprDWdfd) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![Data Warehouse för GDPR hotmodell](images/gdpr-datawarehouse-threat-model.png?raw=true "Data Warehouse för GDPR hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure säkerhet och efterlevnad skissen – GDPR kundens ansvar matrisen](https://aka.ms/gdprCRM) visar en lista över styrenhet och processor ansvar för alla GDPR-artiklar. Observera att för Azure-tjänster en kund är vanligtvis domänkontrollanten och Microsoft fungerar som processorn.

Den [Azure säkerhet och efterlevnad skissen - GDPR Data Warehouse implementering matrisen](https://aka.ms/gdprDW) innehåller information som på vilka GDPR artiklar med hjälp av data warehouse-arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Process för extrahering, transformering och laddning (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Data Warehouse utan att behöva en separat ETL eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsofts business intelligence och analys stack, samt från tredje part-verktyg som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="optional-services"></a>Valfria tjänster
Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagra data formaterade och oformaterad. Följande tjänster kan läggas till i denna Referensarkitektur beroende på kundernas krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid extrahering, transformering och laddning (ETL), extract-load-transform (ELT) och dataintegreringsprojekt. Azure Data Factory har funktioner för att spåra och hitta personliga data, inklusive visualisering och övervakningsverktyg för att identifiera när data har kommit fram och där de kommer ifrån. Med Azure Data Factory kan kunder skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Dessa pipelines gör att kunderna kan hämta data från både interna och externa källor. Kunder kan bearbeta och omvandla data för utdata till datalager som Azure SQL Data Warehouse.
- Kunder kan mellanlagra Ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), vilket gör att infångandet av data med valfri storlek, typ, och inmatning öka hastigheten på en enda plats för driftsanalyser och undersökande analyser.  Azure Data Lake har funktioner som gör att extrahera och konvertering av data. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
