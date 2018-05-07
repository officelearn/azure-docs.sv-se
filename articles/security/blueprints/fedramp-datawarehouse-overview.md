---
title: Azure-säkerhet och efterlevnad modell - datalagret för FedRAMP automatisering
description: Azure-säkerhet och efterlevnad modell - datalagret för FedRAMP automatisering
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure-säkerhet och efterlevnad utkast: datalagret för FedRAMP automatisering

## <a name="overview"></a>Översikt

Den [Federal Risk och auktorisering hanteringsprogram (FedRAMP)](https://www.fedramp.gov/) är ett Förenta staternas myndigheter hela program som ger en standardiserad metod för utvärdering av säkerheten, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure-säkerhet och efterlevnad utkast vägledning för hur man distribuerar en Microsoft Azure data warehouse-arkitektur som hjälper till att implementera en delmängd av FedRAMP hög kontroller. Den här lösningen ger vägledning om distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt som kunder kan använda för att uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund för kunder att Skapa och konfigurera sina egna data warehouse-lösningar i Azure.

Den här referensen för arkitekturen, associerade kontrollen implementeringsguider och hot modeller är avsedda att fungera som en grund för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är tillräckligt för att helt uppfylla kraven för FedRAMP hög baslinjen. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som FedRAMP-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen ger ett data warehouse referensarkitektur som implementerar ett datalager för hög prestanda och säker moln. Det finns två separata datanivåer i den här arkitekturen: en där data som importeras lagras och mellanlagras i en klustrad SQL-miljö och en annan för Azure SQL Data Warehouse där data har lästs in med ett ETL-verktyg (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse, köra analytics i massiv skala.

Microsoft Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen innehåller SQL Server Reporting Services (SSRS) för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafiken krypteras med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Data i Azure SQL Data Warehouse lagras i tabeller med kolumner lagring, ett format som avsevärt minskar lagringskostnaderna data samtidigt förbättra frågeprestanda.  Beroende på kraven för användning, Azure SQL Data Warehouse beräkningsresurser skalas upp eller ned eller stängas helt om det inte finns några aktiva processer som kräver beräkningsresurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, säkerställer hög prestanda. Alla virtuella datorer i denna Referensarkitektur distribuera i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet och katastrofåterställning funktioner.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory (AD)-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större AD-skog struktur, så att kontinuerligt i miljön, även när åtkomst till större skogen är tillgänglig. Alla virtuella datorer är domänanslutna Active Directory-nivån och använda grupprinciper för Active Directory för att tvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå.

En virtuell dator fungerar som en management skyddsmiljö värd, vilket ger en säker anslutning för administratörer att komma åt distribuerade resurser. Data läses in i mellanlagringsområdet via den här värden för hantering av skyddsmiljön. **Azure rekommenderar att konfigurera en VPN- eller Azure ExpressRoute-anslutning för hanterings- och import till referens arkitektur undernät.**

![Datalagret för FedRAMP referens Arkitekturdiagram](images/fedramp-datawarehouse-architecture.png?raw=true "datalagret för FedRAMP referens Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [arkitektur distribution](#deployment-architecture) avsnitt.

Azure Virtual Machines
-   (1) skyddsmiljö värden
-   (2) active Directory-domänkontrollant
-   (2) SQL Server-klusternod
-   (1) SQL Server-vittne

Tillgänglighetsuppsättningar
-   (1) active Directory-domänkontrollanter
-   (1) SQL klusternoderna och vittne

Virtual Network
-   (4) undernät
-   (4) Nätverkssäkerhetsgrupper

SQL Data Warehouse

SQLServer Reporting Services

SQL Azure belastningsutjämnare

Azure Active Directory

Recovery Services-valvet

Azure Key Vault

Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>Arkitektur för distribution

I följande avsnitt beskrivs element för utveckling och implementering.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är en Enterprise Data Warehouse (EDW) som utnyttjar massivt parallell bearbetning (MPP) för att köra komplexa frågor snabbt över petabyte med data. Importera ett stort data till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och använda MPP för att köra högpresterande analytics.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) kan snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer i Azure SQL Data Warehouse.

**Skyddsmiljö värden**: skyddsmiljö värden är den enda post som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö värden ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärråtkomst trafiken från offentliga IP-adresser på listan över säkra. Källan för trafiken måste definieras i den Nätverkssäkerhetsgrupp (NSG) för att tillåta fjärråtkomst fjärrskrivbord (RDP)-trafik.

En virtuell dator har skapats som en värd för domänanslutna skyddsmiljö med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägget](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med hjälp av Azure Key Vault (respekterar Azure Government, PCI DSS, HIPAA och andra krav)
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
Arkitekturen skyddar data i viloläge med hjälp av kryptering, databasen granskning och andra åtgärder.

**Azure Storage** att uppfylla krypterade data på övriga krav, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: serverarbetsbelastningar grupperas i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% SLA för Azure.

**Recovery Services-ventilen**: den [Återställningstjänstvalvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) inrymmer säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i Azure i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, aktivera snabbare återställning.

### <a name="logging-and-audit"></a>Loggning och granskning
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och loggar för kön.
- **Brandväggen loggar**: programmet gatewayen ger fullständig diagnostik och komma åt loggar. Brandväggsloggar är tillgängliga för Programgateway Brandvägg-aktiverade resurser.
- **Logga arkivering**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering med en definierad period 2 dagar. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

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

### <a name="identity-management"></a>Identitetshantering
Följande tekniker hanteringsfunktioner identitet i Azure-miljön:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) kan vara Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.
-   Tillämpningen utförs autentiseringen med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure AD för att autentisera programmet till Azure SQL Database. Mer information finns i så här [skydda känsliga data i SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory-identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkt incidenter att vidta lämpliga åtgärder kan lösas.
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar fokuserar åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumerationen.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL-databasen finns på [Contoso kurs Demo-programmet](https://github.com/Microsoft/azure-sql-security-sample) exempel.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installeras eller köras på den skyddade virtuella datorer.

**Hantering av korrigering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten via vilken uppdaterade distributioner kan skapas för korrigering virtuella datorer när de behövs.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="expressroute-and-vpn"></a>ExpressRoute- och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Kunder kan genom att ange korrekt ExpressRoute eller en VPN-anslutning kan lägga till ett lager av dataskydd under överföring.

### <a name="extract-transform-load-etl-process"></a>Extrahera transformeringen inläsning (ETL) process
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data till Azure SQL Data Warehouse utan att behöva en separat ETL eller importera verktyget. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts affärsinformation och analys stacken, samt fristående verktyg som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Installationsprogram för Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är mycket viktigt att hantera distributionen och etablerar åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till ett befintligt AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
Även om den här data warehouse-arkitektur inte är avsedd för distribution till den [Azure kommersiella](https://azure.microsoft.com/overview/what-is-azure/) miljö, liknande mål kan ske via de tjänster som beskrivs i denna Referensarkitektur samt ytterligare tjänster som är bara tillgängliga i Azure kommersiell miljö. Observera att Azure kommersiella upprätthåller en FedRAMP JAB P-ATO på måttlig effektnivå, vilket gör att myndigheter och partners att distribuera ganska känslig information till molnet genom att använda Azure kommersiell miljö.

Azure kommersiella erbjuder en mängd olika tjänster att referensen formaterad och oformaterat datalagring och mellanlagring som ska användas i informationslager, inklusive:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid extrahera transform-inläsning (ETL) extrahera belastningen transformeringen ELT () och data integration projekt. Med Azure Data Factory kan kunderna skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Kunder kan sedan bearbeta och transformera data för utdata till datalager, till exempel Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) aktiverar insamlingen av data för alla storlek, typ och införandet hastigheten i en enda plats för drifts- och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster, till exempel Azure SQL Data Warehouse.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data (DFD) för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/blueprintdwthreatmodel) eller finns nedan:

![Datalagret för FedRAMP hotmodell](images/fedramp-datawarehouse-threat-model.png?raw=true "datalagret för FedRAMP hotmodell")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad
Den [Azure-säkerhet och efterlevnad utkast – FedRAMP hög kunden ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. På liknande sätt den [Azure-säkerhet och efterlevnad utkast – FedRAMP måttlig kunden ansvar matrisen](https://aka.ms/blueprintcrmmod) visar en lista över alla säkerhetskontroller som krävs av FedRAMP måttlig baslinjen. Båda dokumenten innehåller detaljerad information om implementering av varje kontroll är Microsoft, kund, ansvar eller delas mellan två.

Den [Azure-säkerhet och efterlevnad modell - FedRAMP hög kontroll implementering matrisen](https://aka.ms/blueprintdwcimhigh) och [Azure-säkerhet och efterlevnad modell - FedRAMP måttlig kontrollen implementering matrisen](https://aka.ms/blueprintdwcimmod) ange information som omfattas kontroller av data warehouse-arkitektur för varje FedRAMP baslinje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
