---
title: Handlingsplan för säkerhet och efterlevnad i Azure-Data analys för PCI DSS
description: Handlingsplan för säkerhet och efterlevnad i Azure-Data analys för PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 42ce8bfa78cfa40e147ee90de28c1ac1430070f1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259752"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): Analys för PCI DSS

## <a name="overview"></a>Översikt

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för distribution av en arkitektur för data analys i Azure som hjälper till med kraven på betalnings kort bransch data säkerhets standarder (PCI DSS 3,2). Den presenterar en gemensam referens arkitektur och visar korrekt hantering av kredit korts data (inklusive kort nummer, förfallo datum och verifierings data) i en säker, kompatibel miljö med flera nivåer. Den här skissen visar på vilka sätt kunderna kan uppfylla specifika krav på säkerhet och efterlevnad och fungerar som grund för kunder att bygga och konfigurera egna lösningar för data analys i Azure.

Denna referens arkitektur, implementerings guide och hot modell tillhandahåller en grund för kunderna att uppfylla PCI DSS 3,2-krav. Den här lösningen ger en bas linje som hjälper kunderna att distribuera arbets belastningar till Azure på ett PCI DSS 3,2-kompatibelt sätt. den här lösningen bör dock inte användas i en produktions miljö eftersom ytterligare konfiguration krävs.

Att uppnå PCI DSS-kompatibilitet kräver att en ackrediterad uppfyller kraven enligt (kvalificerad Security-bedömare) certifierar en produktions kund lösning. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

Den här Handlingsplan för säkerhet och efterlevnad i Azure tillhandahåller en analys plattform som kunder kan bygga egna analys verktyg på. Referens arkitekturen beskriver ett allmänt användnings fall där kunder matar in data via Mass data import av SQL/data-administratören eller genom drift data uppdateringar via en drift användare. Båda arbets strömmarna omfattar Azure Functions för att importera data till Azure SQL Database. Azure Functions måste konfigureras av kunden via Azure Portal för att hantera import aktiviteter som är unika för varje kunds behov av analys.

Azure erbjuder en rad olika rapporterings-och analys tjänster för kunderna. Den här lösningen införlivar Azure Machine Learning tjänster tillsammans med Azure SQL Database för att snabbt bläddra igenom data och leverera snabbare resultat via smartare modellering. Azure Machine Learning ökar frågans hastighet genom att identifiera nya relationer mellan data uppsättningar. När data har tränats genom flera statistiska funktioner kan upp till 7 ytterligare lagringspooler (8 totalt inklusive kund servern) synkroniseras med samma tabell modeller för att sprida frågor till arbets belastningar och minska svars tiderna.

För förbättrad analys och rapportering kan Azure SQL-databaser konfigureras med columnstore-index. Både Azure Machine Learning och Azure SQL-databaser kan skalas upp eller ned eller stängas av fullständigt som svar på kund användning. All SQL-trafik krypteras med SSL genom att du kan inkludera självsignerade certifikat. Som bästa praxis rekommenderar Azure användningen av en betrodd certifikat utfärdare för förbättrad säkerhet.

När data har laddats upp till Azure SQL Database och tränas av Azure Machine Learning, sammanfattas de av både den operativa användaren och SQL/data-administratören med Power BI. Power BI visar data intuitivt och drar samman information över flera data uppsättningar för att få bättre insikt. Den höga graden av anpassning och enkel integrering med Azure SQL Database garanterar att kunderna kan konfigurera den för att hantera en mängd olika scenarier som krävs av deras affärs behov.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda data Center för återhämtning. Geografiskt redundant lagring garanterar att data replikeras till ett sekundärt Data Center hundratals mil och igen, som lagras som tre kopior i data centret, vilket förhindrar en negativ händelse i kundens primära Data Center, vilket leder till förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory rollbaserad åtkomst kontroll används för att kontrol lera åtkomsten till distribuerade resurser, inklusive deras nycklar i Azure Key Vault. System hälsan övervakas via Azure Security Center och Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel.

Azure SQL Database hanteras ofta via SQL Server Management Studio (SSMS), som körs från en lokal dator som har kon figurer ATS för att få åtkomst till Azure SQL Database via en säker VPN-eller ExpressRoute-anslutning. **Microsoft rekommenderar att du konfigurerar en VPN-eller ExpressRoute-anslutning för hantering och data import till resurs gruppen referens arkitektur**.

![Analys av diagram över PCI DSS referens arkitektur](images/pcidss-analytics-architecture.png "Analys av diagram över PCI DSS referens arkitektur")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 nätverk
    - (2)/24 nätverk
    - (2) nätverks säkerhets grupper
- Power BI-instrumentpanel

## <a name="deployment-architecture"></a>Distributions arkitektur

I följande avsnitt beskrivs distributions-och implementerings elementen.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) gör det möjligt för kunder att enkelt bygga program med händelsebaserade arkitekturer. Användare väljer den Azure-resurs som de vill prenumerera på och ge händelse hanteraren eller webhook en slut punkt för att skicka händelsen till. Kunder kan skydda webhook-slutpunkter genom att lägga till frågeparametrar till webhook-URL: en när en händelse prenumeration skapas. Azure Event Grid stöder endast HTTPS webhook-slutpunkter. Azure Event Grid gör det möjligt för kunder att kontrol lera åtkomst nivån för olika användare för att utföra olika hanterings åtgärder, till exempel lista händelse prenumerationer, skapa nya och generera nycklar. Event Grid använder sig av rollbaserad åtkomst kontroll i Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) är en server lös beräknings tjänst som gör det möjligt för användare att köra kod på begäran utan att behöva etablera eller hantera infrastruktur. Använd Azure Functions för att köra ett skript eller kod som svar på en rad olika händelser.

**Azure Machine Learning tjänst**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) är en data vetenskaps teknik som gör att datorer kan använda befintliga data för att förutsäga framtida beteenden, resultat och trender.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) gör det enkelt att identifiera och förstå data källor av de användare som hanterar data. Vanliga data källor kan registreras, taggas och sökas efter finansiella data. Data finns kvar på den befintliga platsen, men en kopia av dess metadata läggs till i Data Catalog, tillsammans med en referens till data käll platsen. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

### <a name="virtual-network"></a>Virtuellt nätverk

Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller åtkomst kontrol listor som tillåter eller nekar trafik i ett virtuellt nätverk. Nätverks säkerhets grupper kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå. Följande nätverks säkerhets grupper finns:

  - En nätverks säkerhets grupp för Active Directory
  - En nätverks säkerhets grupp för arbets belastningen

Varje nätverks säkerhets grupp har vissa portar och protokoll öppna så att lösningen kan fungera säkert och korrekt. Dessutom är följande konfigurationer aktiverade för varje nätverks säkerhets grupp:

- [Diagnostikloggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagrings konto
- Azure Monitor loggar är anslutna till [nätverks säkerhets gruppens&#39;diagnostikloggar](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerat med motsvarande nätverks säkerhets grupp.

### <a name="data-in-transit"></a>Data under överföring

Azure krypterar all kommunikation till och från Azure-datacenter som standard. Alla transaktioner som Azure Storage via Azure Portal sker via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md).  Detta hjälper till att skydda och skydda kort korts data i stöd för organisatoriska säkerhets åtaganden och krav på efterlevnad som definieras av PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

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

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

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

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Den identifierar prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå vad användare faktiskt gör med appen. Den är utformad för att hjälpa användarna att kontinuerligt förbättra prestanda och användbarhet.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/PCIAnalyticsTM) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![Analys av diagram över PCI DSS referens arkitektur](images/pcidss-analytics-threat-model.png "Analys för PCI DSS hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
I [matrisen Handlingsplan för säkerhet och efterlevnad i Azure PCI DSS kund ansvar](https://aka.ms/pcidss-analytics-tm) visas ansvar för alla PCI DSS 3,2-krav.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-PCI DSS data analys](https://aka.ms/pcidss-analytics-cim) innehåller information om vilka PCI DSS 3,2-krav som hanteras av arkitekturen för data analys, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.


## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute

En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribueras som en del av den här referens arkitekturen för data analys. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extrahera-transformering-load process
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan läsa in data i Azure SQL Database utan att behöva ett separat verktyg för att extrahera, transformera, läsa in eller importera. PolyBase ger åtkomst till data via T-SQL-frågor. Microsofts Business Intelligence-och analys stack, samt verktyg från andra tillverkare som är kompatibla med SQL Server, kan användas med PolyBase.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med Azure Active Directory i [fyra klick](../../active-directory/hybrid/how-to-connect-install-express.md). Kunder kan också knyta samman den distribuerade Active Directory infrastrukturen (domänkontrollanter) till en befintlig Azure Active Directory genom att göra den distribuerade Active Directory-infrastrukturen till en under domän till en Azure Active Directory skog.

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
