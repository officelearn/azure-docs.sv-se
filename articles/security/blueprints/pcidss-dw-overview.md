---
title: Handlingsplan för säkerhet och efterlevnad i Azure informations lager för PCI DSS
description: Handlingsplan för säkerhet och efterlevnad i Azure informations lager för PCI DSS
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 95e8dbdbb1a3adadb6b81c8d9023622de4cc3058
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259757"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): Informations lager för PCI DSS

## <a name="overview"></a>Översikt

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för distribution av en data lager arkitektur i Azure som hjälper dig att uppfylla kraven på betalnings kort bransch data säkerhets standarder (PCI DSS 3,2). Den presenterar en gemensam referens arkitektur och visar korrekt hantering av kredit korts data (inklusive kort nummer, förfallo datum och verifierings data) i en säker, kompatibel miljö med flera nivåer. Den här skissen visar på vilka sätt kunderna kan uppfylla specifika krav på säkerhet och efterlevnad och fungerar som grund för kunder att bygga och konfigurera sina egna data lager lösningar i Azure.

Denna referens arkitektur, implementerings guide och hot modell tillhandahåller en grund för kunderna att uppfylla PCI DSS 3,2-krav. Den här lösningen ger en bas linje som hjälper kunderna att distribuera arbets belastningar till Azure på ett PCI DSS 3,2-kompatibelt sätt. den här lösningen bör dock inte användas i en produktions miljö eftersom ytterligare konfiguration krävs.

Att uppnå PCI DSS-kompatibilitet kräver att en ackrediterad uppfyller kraven enligt (kvalificerad Security-bedömare) certifierar en produktions kund lösning. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här lösningen ger en referens arkitektur som implementerar ett informations lager med hög prestanda och säker moln lagring. Det finns två separata data nivåer i den här arkitekturen: en där data importeras, lagras och mellanlagras i en klustrad SQL-miljö, och en annan för den Azure SQL Data Warehouse där data läses in med ett extraherings-, transformerings-eller load-verktyg (t. ex. [PolyBase ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-frågor) för bearbetning. När data lagras i Azure SQL Data Warehouse kan analyser köras i en enorm skala.

Azure erbjuder en rad olika rapporterings-och analys tjänster för kunden. Den här lösningen innehåller SQL Server Reporting Services (SSRS) för snabb skapandet av rapporter från Azure SQL Data Warehouse. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

Data i Azure SQL Data Warehouse lagras i Relations tabeller med lagring i kolumner, ett format som avsevärt minskar kostnaderna för data lagring samtidigt som du förbättrar prestandan för frågor. Beroende på användnings kraven kan Azure SQL Data Warehouse beräknings resurser skalas upp eller ned eller stängas av helt om det inte finns några aktiva processer som kräver beräknings resurser.

En SQL-Load Balancer hanterar SQL-trafik, vilket säkerställer höga prestanda. Alla virtuella datorer i den här referens arkitekturen distribueras i en tillgänglighets uppsättning med SQL Server instanser som kon figurer ATS i en tillgänglighets grupp som alltid är tillgängliga för funktioner med hög tillgänglighet och katastrof återställning.

Denna data lager referens arkitektur innehåller också en Active Directory nivå för hantering av resurser i arkitekturen. Active Directory under nätet gör det enkelt att införa en större Active Directory skogs struktur, vilket möjliggör kontinuerlig drift av miljön även när åtkomst till den större skogen inte är tillgänglig. Alla virtuella datorer är domänanslutna till Active Directory nivån och använder Active Directory grup principer för att tillämpa konfigurationer för säkerhet och efterlevnad på operativ system nivå.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda data Center för återhämtning. Geografiskt redundant lagring garanterar att data replikeras till ett sekundärt Data Center hundratals mil och igen, som lagras som tre kopior i data centret, vilket förhindrar en negativ händelse i kundens primära Data Center, vilket leder till förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory rollbaserad åtkomst kontroll används för att kontrol lera åtkomsten till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. System hälsan övervakas via Azure Security Center och Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel.

En virtuell dator fungerar som en hanterings skydds-värd, vilket ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Data läses in i mellanlagringsområdet via den här hanterings skydds-värden. **Microsoft rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.**

![Diagram över data lager för PCI DSS referens arkitektur](images/pcidss-dw-architecture.png "Diagram över data lager för PCI DSS referens arkitektur")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Tillgänglighetsuppsättningar
    - Active Directory-domän kontrollanter
    - SQL-klusternoder och vittne
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
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

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) är ett informations lager för företag (analyslösningar) som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte data, vilket gör det möjligt för användare att effektivt identifiera finansiella data. Användare kan använda enkla PolyBase T-SQL-frågor för att importera Big data till SQL Data Warehouse och utnyttja kraften i MPP för att köra analys med höga prestanda.

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) ger snabb generering av rapporter med tabeller, diagram, kartor, mätare, matriser och mycket mer för Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) gör det enkelt att identifiera och förstå data källor av de användare som hanterar data. Vanliga data källor kan registreras, taggas och sökas efter finansiella data. Data finns kvar på den befintliga platsen, men en kopia av dess metadata läggs till i Data Catalog, tillsammans med en referens till data käll platsen. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger användare åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärran sluten trafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure-diagnostik tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av virtuella dator resurser när den inte används
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs.

### <a name="virtual-network"></a>Virtuellt nätverk

Den här referens arkitekturen definierar ett privat virtuellt nätverk med adress utrymmet 10.0.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. Nätverks säkerhets grupper kan användas för att skydda trafik i ett undernät eller på en enskild virtuell dator nivå. Följande nätverks säkerhets grupper finns:

  - En nätverks säkerhets grupp för data skiktet (SQL Server kluster, SQL Server vittne och SQL Load Balancer)
  - En nätverks säkerhets grupp för hanterings skydds-värden
  - En nätverks säkerhets grupp för Active Directory
  - En nätverks säkerhets grupp för SQL Server Reporting Services

Varje nätverks säkerhets grupp har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverks säkerhets grupp:

- [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
- Azure Monitor loggar är anslutna till [nätverks säkerhets gruppens&#39;diagnostikloggar](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerat med motsvarande nätverks säkerhets grupp.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila genom flera åtgärder, inklusive kryptering och databas granskning.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Detta hjälper till att skydda och skydda kort korts data i stöd för organisatoriska säkerhets åtaganden och krav på efterlevnad som definieras av PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**Azure SQL Database**: Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:

- [Active Directory autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
- [SQL Database Auditing](../../sql-database/sql-database-auditing.md) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
- Azure SQL Database har kon figurer ATS för att använda [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte omfattas av obehörig åtkomst.
- [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
- Med [SQL hot identifiering](../../sql-database/sql-database-threat-detection.md) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
- [Krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [Utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kan användas för att avbryta bearbetningen av data ämnen, eftersom det gör det möjligt för användare att lägga till anpassade egenskaper till databas objekt och tagga data som "utgångna" för att stödja program logiken för att förhindra bearbetning av associerade finansiella data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) gör det möjligt för användare att definiera principer för att begränsa åtkomsten till data för att avbryta bearbetningen.
- [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar känslig data exponering genom att maskera data till icke-privilegierade användare eller program. Dynamisk data maskning kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta hjälper till att identifiera och minska åtkomsten till data så att den inte avslutar databasen via obehörig åtkomst. Kunderna ansvarar för att justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker tillhandahåller funktioner för att hantera åtkomst till data i Azure-miljön:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsoft&#39;s molnbaserad, molnbaserad katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som har åtkomst till Azure SQL Database.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Dessutom använder databasens kolumn kryptering Azure Active Directory för att autentisera programmet att Azure SQL Database. Mer information finns i så här [skyddar du känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Med [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en&#39;organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som är&#39;relaterade till en organisations identiteter. undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet

**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda och komma åt sådana data:

- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med rollbaserad åtkomst kontroll.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för virtuella datorer ger real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara, med konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera eller kör på skyddade virtuella datorer.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Server arbets belastningarna grupperas i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

**Recovery Services valv**: [Recovery Services valvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av virtuella Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställnings tider.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp inom Log Analytics arbets ytor, vilket innebär att alla data kan analyseras tillsammans oavsett den ursprungliga källan. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](../../azure-monitor/insights/ad-assessment.md): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från Azure SQL Database. Med Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen kan kunder enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/pcidss-dw-tm) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![Diagram över data lager för PCI DSS referens arkitektur](images/pcidss-dw-threat-model.png "PaaS-webbprogram för PCI DSS hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – PCI DSS kund ansvar](https://aka.ms/pcidss-crm) visas ansvar för alla PCI DSS 3,2-krav.

[Matrisen handlingsplan för säkerhet och efterlevnad i Azure-PCI DSS data lager implementering](https://aka.ms/pcidss-dw-cim) innehåller information om vilka PCI DSS 3,2-krav som hanteras av data lager arkitekturen, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute

En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribueras som en del av den här data lagrets referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extrahera-transformering-load process

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Data Warehouse utan att behöva ett separat verktyg för att extrahera, transformera, läsa in eller importera. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts Business Intelligence-och analys stack, samt verktyg från andra tillverkare som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med Azure Active Directory i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md). Kunder kan också knyta samman den distribuerade Active Directory infrastrukturen (domänkontrollanter) till en befintlig Azure Active Directory genom att göra den distribuerade Active Directory-infrastrukturen till en under domän till en Azure Active Directory skog.

### <a name="optional-services"></a>Valfria tjänster

Azure erbjuder en mängd olika tjänster som hjälper till med lagring och mellanlagring av formaterade och oformaterade data. Följande tjänster kan läggas till i den här referens arkitekturen, beroende på kund krav:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) är en hanterad moln tjänst som skapats för komplexa hybrid extraherings-och data integrerings projekt. Azure Data Factory har funktioner som hjälper dig att spåra och hitta kort korts data, inklusive visualiserings-och övervaknings verktyg för att identifiera när data har anlänt och var de kom från. Med hjälp av Azure Data Factory kan kunder skapa och schemalägga data drivna arbets flöden som kallas pipelines som matar in data från olika data lager. Dessa pipelines gör att kunder kan mata in data från både interna och externa källor. Kunderna kan sedan bearbeta och transformera data för utdata till data lager, t. ex. Azure SQL Data Warehouse.
- Kunder kan mellanlagra ostrukturerade data i [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), vilket gör det möjligt att samla in data i valfri storlek, typ och inmatnings hastighet på en enda plats för operativa och undersökande analyser.  Azure Data Lake har funktioner som möjliggör extrahering och konvertering av data. Azure Data Lake Store är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet och kan integreras snyggt med andra Azure-tjänster som Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
