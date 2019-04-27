---
title: Azure-säkerhet och efterlevnad skissen - datalagret för FedRAMP Automation
description: Azure-säkerhet och efterlevnad skissen - datalagret för FedRAMP Automation
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586040"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure Blueprint för säkerhet och efterlevnad: Data Warehouse för FedRAMP Automation

## <a name="overview"></a>Översikt

Den [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) är ett program för USA regeringen som ger en standardiserad metod för säkerhetsbedömningar, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure Blueprint för säkerhet och efterlevnad innehåller anvisningar för hur man distribuerar en Microsoft Azure data warehouse-arkitektur som hjälper dig att implementera en delmängd av FedRAMP High-kontroller. Den här lösningen innehåller råd om distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att Skapa och konfigurera sina egna data warehouse-lösningar i Azure.

Den här referensarkitekturen och tillhörande kontrollen implementeringsguider hotmodeller är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Distribuera ett program i den här miljön utan ändringar är tillräckligt för att helt uppfylla kraven för FedRAMP High-baslinje. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett sätt som FedRAMP-kompatibla.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen ger en data warehouse-referensarkitektur som implementerar ett informationslager i molnet med höga prestanda och säker. Det finns två separata datanivåer i den här arkitekturen: en där data har importerats, lagras och mellanlagras i en klustrad SQL-miljö och en annan för Azure SQL Data Warehouse där data läses med hjälp av ett ETL-verktyg (t.ex. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan köra analyser i massiv skala.

Microsoft Azure erbjuder en mängd olika tjänster för rapportering och analys för kunden. Den här lösningen inkluderar SQL Server Reporting Services (SSRS) för att snabbt skapa rapporter från Azure SQL Data Warehouse. Alla SQL-trafik är krypterad med SSL genom Inkluderingen av självsignerade certifikat. Som bästa praxis rekommenderar Azure användning av en betrodd certifikatutfärdare för ökad säkerhet.

Data i Azure SQL Data Warehouse lagras i Relationstabeller med kolumnbaserad lagring, ett format som avsevärt minskar lagringskostnaderna för data samtidigt förbättra frågeprestanda.  Beroende på användningskrav beräkningsresurser i Azure SQL Data Warehouse skalas upp eller ned eller stänga av helt om det finns inga aktiva processer som kräver beräkningsresurser.

En SQL-belastningsutjämnare hanterar SQL-trafik, att säkerställa hög prestanda. Alla virtuella datorer i den här referensarkitekturen distribuerar i en tillgänglighetsuppsättning med SQL Server-instanser som konfigurerats i en AlwaysOn-tillgänglighetsgrupp för funktioner för hög tillgänglighet och katastrofåterställning.

Denna Referensarkitektur för data warehouse innehåller även en Active Directory (AD)-nivå för hantering av resurser i arkitekturen. Active Directory-undernät kan enkelt införande under en större AD-skog struktur, så att för kontinuerlig drift av miljön även om åtkomst till större skogen är inte tillgänglig. Alla virtuella datorer är domänansluten till Active Directory-nivå och använda grupprinciper för Active Directory för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

En virtuell dator fungerar som en management skyddsmiljö-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data har lästs in i mellanlagringsområdet via den här management skyddsmiljö-värd. **Azure rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![Data Warehouse för FedRAMP-referens för Arkitekturdiagram](images/fedramp-datawarehouse-architecture.png?raw=true "Data Warehouse för Arkitekturdiagram för FedRAMP-referens")

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

Azure Virtual Machines
-   (1) skyddsmiljö-värd
-   (2) active Directory-domänkontrollant
-   (2) SQL Server-klusternod
-   (1) SQL Server-vittne

Tillgänglighetsuppsättningar
-   (1) active Directory-domänkontrollanter
-   (1) SQL-klusternoder och vittne

Virtual Network
-   (4) undernät
-   (4) Nätverkssäkerhetsgrupper

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Recovery Services Vault

Azure Key Vault

Azure Monitor-loggar

## <a name="deployment-architecture"></a>Distributionsarkitektur för

I följande avsnitt finns element för utveckling och implementering.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är en Enterprise Data mpp-modell som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte med data. Importera stordata till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och använder kraften i MPP för att köra högpresterande analyser.

**SQLServer Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) kan snabbt skapa rapporter med tabeller, diagram, kartor, mätare, matriser och mer för Azure SQL Data Warehouse.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i den Nätverkssäkerhetsgrupp (NSG) för att tillåta (RDP) trafik för fjärrskrivbordet.

En virtuell dator har skapats som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Monitor loggar tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault (värnar om Azure Government, PCI DSS, HIPAA och andra krav)
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
  - Azure Monitor-loggar är ansluten till den [NSG-diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage** att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database** Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av data och loggfiler att skydda information i vila. TDE ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt data i klartext.
-   [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter referensarkitekturen distribuerar. Kunder kommer att behöva justera dynamisk datamaskning inställningar för att uppfylla sina databasschemat.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

### <a name="logging-and-audit"></a>Loggning och granskning
[Azure Monitor-loggar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet samt filsystemets hälsa. Den [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Loggarna finns Windows-händelsesystemloggar och Azure Blob storage, tabeller och köloggar.
- **Brandväggen loggar**: Application Gateway ger fullständig diagnostik och komma åt loggar. Brandväggsloggar är tillgängliga för WAF-aktiverade Application Gateway-resurser.
- **Arkivera loggen**: Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering med en definierad period av 2 dagar. Dessa loggar ansluta till Azure Monitor-loggar för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom kan ingår följande övervakningslösningar som en del av den här arkitekturen:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Instrumentpanelen för säkerhet och granskning ger en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Lösningen för uppdateringshantering kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Lösningen ändringsspårning kan kunder enkelt kan identifiera ändringar i miljön.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker hanteringsfunktioner identitet i Azure-miljön:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) kan vara Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure AD för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar fokuserar åtkomsthantering för Azure. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL Database finns i [programmet Contoso Clinic Demo](https://github.com/Microsoft/azure-sql-security-sample) exemplet.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="expressroute-and-vpn"></a>ExpressRoute och VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller en säker VPN-tunnel måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för data warehouse. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Kunder kan genom att på lämpligt sätt att konfigurera ExpressRoute eller VPN kan lägga till ett lager säkerhet för data under överföring.

### <a name="extract-transform-load-etl-process"></a>Process för extrahering, transformering och laddning (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Data Warehouse utan att behöva en separat ETL eller importera verktyget. PolyBase kan åtkomst till data via T-SQL-frågor. Microsofts business intelligence och analys stack, samt från tredje part-verktyg som är kompatibla med SQL Server kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med AAD i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Kunder kan också koppla distribuerade Active Directory-infrastruktur (domänkontrollanter) till en befintlig AAD genom att göra den distribuerade Active Directory-infrastrukturen en underdomän till en AAD-skog.

### <a name="additional-services"></a>Ytterligare tjänster
Även om den här arkitekturen för data warehouse inte är avsedd för distribution till den [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) miljö, liknande kan ske via tjänster som beskrivs i denna Referensarkitektur samt ytterligare tjänster som är endast tillgängliga i Azure kommersiell miljö. Observera att Azure Commercial finns kvar en FedRAMP JAB P-ATO med begränsad inverkan, vilket gör att myndigheter och partner att distribuera mindre känslig information till molnet att utnyttja Azure kommersiell miljö.

Azure kommersiella erbjuder en mängd olika tjänsterna som referens formaterad och oformaterad datalagring och mellanlagring som ska användas i datalagring, inklusive:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad molntjänst som är utformat för komplexa hybrid extrahering, transformering och laddning (ETL), extract-load-transform (ELT) och dataintegreringsprojekt. Med Azure Data Factory kan kunder skapa och schemalägga datadrivna arbetsflöden som kallas pipelines som mata in data från olika datalager. Kunder kan bearbeta och omvandla data för utdata till datalager som Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) möjliggör insamlingen av data av alla storlekar, typer och datainmatning i en enda plats för drifts- och undersökande analyser. Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet och snyggt integrerat med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram (DFD) för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/blueprintdwthreatmodel) eller finns nedan:

![Data Warehouse för FedRAMP hotmodell](images/fedramp-datawarehouse-threat-model.png?raw=true "Data Warehouse för FedRAMP hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den [Azure Security Blueprint och efterlevnad – FedRAMP hög kundens ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP High-baslinje. På samma sätt kan den [Azure Security Blueprint och efterlevnad – FedRAMP måttlig kundens ansvar matrisen](https://aka.ms/blueprintcrmmod) visar en lista över alla säkerhetskontroller som krävs av FedRAMP måttlig baslinjen. Både dokument detaljer om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - FedRAMP hög kontroll implementering matrisen](https://aka.ms/blueprintdwcimhigh) och [Azure säkerhet och efterlevnad skissen - FedRAMP begränsad kontroll implementering matrisen](https://aka.ms/blueprintdwcimmod) ger information som omfattas kontroller av data warehouse-arkitektur för varje FedRAMP-baslinje, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
