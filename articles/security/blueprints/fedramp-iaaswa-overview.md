---
title: Handlingsplan för säkerhet och efterlevnad i Azure-IaaS-webbprogram för FedRAMP
description: Handlingsplan för säkerhet och efterlevnad i Azure-IaaS-webbprogram för FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: a0458525eaf985ac6b1ff2afde5726bbac45b4f2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778777"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): IaaS-webbprogram för FedRAMP

## <a name="overview"></a>Översikt

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov) är ett program för amerikanska myndigheter som tillhandahåller en standardiserad metod för säkerhetsbedömning, auktorisering och kontinuerlig övervakning av moln produkter och tjänster. Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation ger vägledning för distribution av en IaaS-miljö (FedRAMP Infrastructure as a Service) som lämpar sig för ett enkelt webb program som riktar sig mot Internet. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en gemensam referens arkitektur och demonstrerar hur kunder kan uppfylla specifika krav på säkerhet och efterlevnad och fungerar som en grund för kunder att bygga och Konfigurera egna lösningar på Azure. Lösningen implementerar en delmängd av kontroller från FedRAMP hög bas linje, baserat på NIST SP 800-53. Mer information om FedRAMP-krav och den här lösningen finns i [dokumentationen för efterlevnad](#compliance-documentation).
> [!NOTE]
> Den här lösningen distribuerar till Azure Government.

Den här Handlingsplan för säkerhet och efterlevnad i Azure-Automation distribuerar automatiskt en IaaS-webbprograms referens arkitektur med förkonfigurerade säkerhets kontroller för att hjälpa kunderna att uppnå efterlevnad med FedRAMP-krav. Lösningen består av Azure Resource Manager mallar och PowerShell-skript som hjälper till att distribuera och konfigurera resurser.

Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö. Det räcker inte att distribuera ett program i den här miljön utan modifiering för att helt uppfylla kraven i FedRAMP hög bas linje. Observera följande:
- Den här arkitekturen tillhandahåller en bas linje som hjälper kunder att använda Azure på ett FedRAMPt sätt.
- Kunderna ansvarar för att utföra lämpliga bedömningar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

En snabb översikt över hur den här lösningen fungerar finns i den här [videon](https://aka.ms/fedrampblueprintvideo) som förklarar och demonstrerar distributionen.

Klicka [här](https://aka.ms/fedrampblueprintrepo) om du vill ha distributions anvisningar.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här lösningen distribuerar en referens arkitektur för ett IaaS-webbprogram med en SQL Server Server del. Arkitekturen omfattar en webb nivå, en data nivå, Active Directory infrastruktur, Application Gateway och Load Balancer. Virtuella datorer som distribueras till webben och data nivåer konfigureras i en tillgänglighets uppsättning och SQL Server instanser konfigureras i en AlwaysOn-tillgänglighetsgrupper för hög tillgänglighet. Virtuella datorer är domänanslutna och Active Directory grup principer används för att genomdriva konfigurationer för säkerhet och efterlevnad på operativ system nivå. En skydds-värd ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. **Azure rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.**

![IaaS webb program för FedRAMP referens arkitektur diagram](images/fedramp-iaaswa-architecture.png?raw=true "IaaS webb program för FedRAMP referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Azure virtuella maskiner
    - (1) skydds-värd (Windows Server 2016 Data Center)
    - (2) Active Directory domänkontrollant (Windows Server 2016 Data Center)
    - (2) SQL Server klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Tillgänglighetsuppsättningar
    - (1) Active Directory domänkontrollanter
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Virtual Network
    - (1)/16 virtuella nätverk
    - (5)/24 undernät
    - DNS-inställningar är inställda på båda domän kontrol Lanterna
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway aktive rad
        - brand Väggs läge: skydd
        - regel uppsättning: OWASP 3,0
        - lyssnare: port 443
- Azure Storage
    - (7) geo-redundanta lagrings konton
- Azure Cloud Witness
- Recovery Services-valv
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (loggar)

## <a name="deployment-architecture"></a>Distributions arkitektur

I följande avsnitt beskrivs utvecklings-och implementerings elementen.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Skydds-värdens NSG tillåter endast anslutningar på TCP-port 3389 för RDP. Kunder kan ytterligare konfigurera skydds-värden för att uppfylla organisationens system härdnings krav.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: Den här lösningen distribuerar resurser i en arkitektur med ett separat webb under nät, databas under nät, Active Directory undernät och hanterings under nät i ett virtuellt nätverk. Undernät separeras logiskt av regler för nätverks säkerhets grupper som tillämpas på enskilda undernät för att begränsa trafiken mellan undernät till endast det som krävs för system-och hanterings funktioner.

Se konfigurationen för [nätverks säkerhets grupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) som distribueras med den här lösningen. Kunder kan konfigurera nätverks säkerhets grupper genom att redigera filen ovan med hjälp av [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som en guide.

Varje undernät har en dedikerad nätverks säkerhets grupp (NSG):
- 1 NSG för Application Gateway (LBNSG)
- 1 NSG för skydds-värden (MGTNSG)
- 1 NSG för primära domänkontrollanter och reservdomänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar (SQLNSG)
- 1 NSG för webb nivå (WEBNSG)

**Undernät**: Varje undernät är associerat med motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med hjälp av flera krypterings åtgärder.

**Azure Storage**: Alla lagrings konton använder [kryptering för lagringstjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)för att uppfylla kraven på vilande kryptering.

**SQL Server**: SQL Server har kon figurer ATS för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), som utför kryptering och dekryptering i real tid av data och loggfiler för att skydda information i vila. TDE garanterar att lagrade data inte har blivit föremål för obehörig åtkomst.

Kunder kan också konfigurera följande SQL Server säkerhets åtgärder:
-   [Autentisering och auktorisering i AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) möjliggör identitets hantering av databas användare och andra Microsoft-tjänster på en central plats.
-   [SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databas händelser och skriver dem till en Gransknings logg i ett Azure Storage-konto.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   Med [SQL hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) kan du identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar för misstänkta databas aktiviteter, potentiella sårbarheter, SQL-injektering och avvikande databas åtkomst mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller App-servrar med åtkomst till nycklarna komma åt oformaterade data.
-   [SQL Database dynamisk data maskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter att referens arkitekturen har distribuerats. Kunderna måste justera inställningarna för dynamisk datamaskering för att följa deras databas schema.

**Azure Disk Encryption**: Azure Disk Encryption används för att kryptera Windows IaaS-diskar för virtuella datorer. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypterings nycklarna.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker tillhandahåller funktioner för identitets hantering i Azure-miljön:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare.
- Autentisering till ett kunddistribuerat webb program kan utföras med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Med [Azure Role-baserade Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) kan du prioriterad åtkomst hantering för Azure. Åtkomst till prenumerationen är begränsad till prenumerations administratören och åtkomsten till resurser kan begränsas baserat på användar rollen.
- En distribuerad IaaS Active Directory-instans tillhandahåller identitets hantering på operativ system nivå för distribuerade IaaS-virtuella datorer.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Azure Key Vault hjälper till att hantera IaaS-nycklar och hemligheter för disk kryptering för virtuella datorer.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras med den här Handlingsplan för säkerhet och efterlevnad i Azure Automation konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen distribuerar även Azure Automation-lösningen genom vilken uppdaterings distributioner kan skapas för att distribuera korrigeringsfiler till Windows-servrar vid behov.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för Virtual Machines ger real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara, med konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera eller kör på skyddade virtuella datorer.

**Application Gateway**: Arkitekturen minskar risken för säkerhets problem med en Application Gateway med brand vägg för webbaserade program (WAF) och OWASP-ruleset är aktive rad. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) -avlastning
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brand vägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF-läge)
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0 ruleset

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA. Lösningen distribuerar alla virtuella datorer på webb nivå och data skikt i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighets uppsättningar säkerställer att de virtuella datorerna distribueras över flera isolerade maskin varu kluster för att förbättra tillgängligheten. Dessutom distribuerar den här lösningen SQL Server virtuella datorer i en tillgänglighets uppsättning som en [AlwaysOn-tillgänglighetsgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Funktionen Always on tillgänglighets grupp tillhandahåller funktioner för hög tillgänglighet och katastrof återställning.

**Recovery Services valv**: [Recovery Services Vault](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av Azure-Virtual Machines i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställnings tider.

**Moln vittne**: [Moln vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av ett klusterkvorum i Windows Server 2016 som utnyttjar Azure som medlings punkt. Moln vittnet, precis som alla andra kvorumresurser, får en röst och kan delta i kvorumkonfigurationen, men använder allmänt tillgängliga Azure-Blob Storage. Detta eliminerar den extra underhålls kostnaden för virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure Monitor loggar innehåller omfattande loggning av system-och användar aktiviteter, samt system hälsan. Lösningen [Azure Monitor loggar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) samlar in och analyserar data som genererats av resurser i Azure och lokala miljöer.

- **Aktivitets loggar:**  [Aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar:**  [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som genereras av varje resurs. Loggarna omfattar Windows händelse system loggar, Azure Storage-loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar.
- **Logg arkivering:**  Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning. Dessa loggar ansluter till Azure Monitor loggar för bearbetning, lagring och instrument panels rapportering.

Dessutom installeras följande övervaknings lösningar som en del av den här arkitekturen. Observera att det är kundens ansvar att konfigurera dessa lösningar så att de överensstämmer med FedRAMP säkerhets kontroller:
-   [AD-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Lösningen mot skadlig kod rapporterar om skadlig kod, hot och skydds status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation lösning lagrar, kör och hanterar Runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Säkerhet och granskning-instrumentpanelen ger bättre inblick i säkerhets läget för resurser genom att tillhandahålla mått på säkerhets domäner, viktiga problem, identifieringar, Hot information och vanliga säkerhets frågor.
-   [SQL-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Med den Uppdateringshantering lösningen kan kund hantering av säkerhets uppdateringar för operativ systemet, inklusive status för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Azure-aktivitets loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Med Ändringsspårning-lösningen kan kunder enkelt identifiera ändringar i miljön.

AzureMonitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i kunders Azure-resurser.

## <a name="threat-model"></a>Hot modell
Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/fedrampWAdfd) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![IaaS webb program för FedRAMP hot modell](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS webb program för FedRAMP hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP hög kund ansvar](https://aka.ms/blueprinthighcrm) visas alla säkerhets kontroller som krävs av den höga bas linjen för FedRAMP. Matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-FedRAMP IaaS för webb program för hög kontroll](https://aka.ms/blueprintwacim) visas alla säkerhets kontroller som krävs av den höga bas linjen FedRAMP. Matrisen innehåller information om vilka kontroller som omfattas av arkitekturen för IaaS-webbprogram, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Managers API-tjänst för att distribuera resurser i Azure. Detaljerade distributions anvisningar finns [här](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Den här lösningen distribuerar till Azure Government.

#### <a name="quickstart"></a>Snabbstart
1. Klona eller ladda ned [den här](https://aka.ms/fedrampblueprintrepo) GitHub-lagringsplatsen till din lokala arbets Station.

2. Kör PowerShell-skriptet för distribution: Azure-Blueprint/Predeploy/Orchestration_InitialSetup. ps1.

3. Klicka på knappen nedan och logga in på Azure Portal, ange de nödvändiga parametrarna för ARM-mallen och klicka på **köp**.

    [![Distribuera till Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här IaaS-webbprogrammets referens arkitektur. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.  
- Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.  
- Kunder får kopiera och använda det här dokumentet för intern referens.  
- Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.  
- Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
- Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
