---
title: IaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure
description: IaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 1cceecba59b4cd1a70fc6f152020757e137f4d45
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778988"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>IaaS-webbprogram för Australien-skydd Handlingsplan för säkerhet och efterlevnad i Azure

## <a name="overview"></a>Översikt
Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för distribution av en IaaS-miljö (Infrastructure as a Service) som är lämplig för insamling, lagring och hämtning av AU-skyddade myndighets data som är kompatibla med målen för den australiska regeringens informations hand bok (ISM) som produceras av australiskt Signals direktorat (ASD). Den här skissen visar en gemensam referens arkitektur och hjälper till att visa korrekt hantering av känsliga myndighets data i en säker, kompatibel miljö med flera nivåer.

Denna referens arkitektur, implementerings guide och hot modell tillhandahåller en grund för kunderna att utföra egna planerings-och system ackrediterings processer, vilket hjälper kunderna att distribuera arbets belastningar till Azure på ett ASD-kompatibelt sätt. Kunder kan välja att implementera en Azure-VPN Gateway eller ExpressRoute för att använda federerade tjänster och integrera lokala resurser med Azure-resurser. Kunderna måste fundera på säkerhets konsekvenserna av att använda lokala resurser. Ytterligare konfiguration krävs för att uppfylla alla krav, eftersom de kan variera beroende på vad som gäller för varje kunds implementering.

För att kunna använda ASD-kompatibilitet krävs att en information Security-registrerad bedömaren granskar systemet. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som har skapats med den här arkitekturen, eftersom kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här lösningen distribuerar en referens arkitektur för ett IaaS-webbprogram med en SQL Server Server del. Arkitekturen omfattar en webb nivå, en data nivå, Active Directory infrastruktur, Application Gateway och Load Balancer. Virtuella datorer som distribueras till webb-och data nivåerna konfigureras i en tillgänglighets uppsättning och SQL Server instanser konfigureras i en tillgänglighets grupp som alltid är tillgängliga för hög tillgänglighet. Virtuella datorer är domänanslutna och Active Directory grup principer används för att genomdriva konfigurationer för säkerhet och efterlevnad på operativ system nivå. En skydds-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser.

Arkitekturen kan leverera en säker hybrid miljö som utökar ett lokalt nätverk till Azure, vilket gör att webbaserade arbets belastningar kan nås på ett säkert sätt av företags användare i en organisations privata lokala nätverk eller från Internet. För lokala lösningar är kunden både konto ansvarig och ansvarig för alla aspekter av säkerhet, åtgärder och efterlevnad.

De Azure-resurser som ingår i den här lösningen kan ansluta till en lokal nätverks-eller datacenter-plats (t. ex. CDC i Canberra) via IPSec VPN med hjälp av Azure-VPN Gateway eller via ExpressRoute.. Beslut om att använda en VPN-anslutning bör göras med klassificeringen av överförda data och nätverks Sök vägen i åtanke. Kunder som kör storskaliga och verksamhets kritiska arbets belastningar med Big data-krav bör överväga en hybrid nätverks arkitektur med ExpressRoute för privat nätverks anslutning till Azure-tjänster. Mer information om anslutnings metoder till Azure finns i avsnittet vägledning och rekommendationer.

Federation med Azure Active Directory ska användas för att göra det möjligt för användare att autentisera med lokala autentiseringsuppgifter och få åtkomst till alla resurser i molnet med hjälp av en lokal Active Directory Federation Services (AD FS)-infrastruktur. Active Directory Federation Services (AD FS) kan tillhandahålla enkla, säkra identitets Federations-och webb funktioner för enkel inloggning för den här hybrid miljön. Mer information Azure Active Directory-installationen finns i avsnittet vägledning och rekommendationer.

I lösningen används Azure Storage-konton som kunder kan konfigurera för att använda Kryptering för lagringstjänst för att upprätthålla konfidentialiteten för data i vila. Azure lagrar tre kopior av data inom en kunds valda region för återhämtning. Azure-regioner distribueras i elastiska region par och geografiskt redundant lagring garanterar att data replikeras till den andra regionen med tre kopior. Detta förhindrar en negativ händelse på kundens primära data plats, vilket leder till förlust av data.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory rollbaserad åtkomst kontroll används för att kontrol lera åtkomsten till distribuerade resurser och nycklar i Azure Key Vault. System hälsan övervakas via Azure Security Center och Azure Monitor. Kunderna konfigurerar både övervaknings tjänster för att avbilda loggar och Visa system hälsan på en enda, enkel portalerna instrument panel. Azure Application Gateway konfigureras som en brand vägg i skydds läge och kräver TLS-trafik med en lägsta version av 1,2.

![IaaS webb program för au-skyddad referens arkitektur](images/au-protected-iaaswa-architecture.png?raw=true "IaaS webb program för au-skyddat referens arkitektur diagram") 

Den här lösningen använder följande Azure-tjänster. Mer information finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Tillgänglighetsuppsättningar
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) brand vägg för webbaserade program
        - Brand Väggs läge: skydd
        - Regel uppsättning: OWASP 3,0
        - Lyssnar port: 443
- Azure Cloud Witness
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Monitor-loggar
- Azure Storage
- Azure virtuella maskiner
    - (1) hantering/skydds (Windows Server 2016 Data Center)
    - (2) SQL Server klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) nätverks säkerhets grupper
    - Azure Network Watcher
- Recovery Services valv

Den här skissen innehåller Azure-tjänster som inte har certifierats för användning på den skyddade klassificeringen av australisk cyberhot Security Centre (ACSC). Alla tjänster som ingår i denna referens arkitektur har certifierats av ACSC på DLM-nivån (spridnings begränsnings markörer). Microsoft rekommenderar att kunderna granskar publicerade säkerhets-och gransknings rapporter som är relaterade till dessa Azure-tjänster och använder sina riskhanterings ramverk för att avgöra om Azure-tjänsten är lämplig för intern ackreditering och användning på Skyddad klassificering.

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Skydds-värd**: Skydds-värden är den enda post adressen som ger användare åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärran sluten trafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen.

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure-diagnostik tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) att använda Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av virtuella dator resurser när den inte används

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: Den här lösningen distribuerar resurser i en arkitektur med ett separat webb under nät, databas under nät, Active Directory undernät och hanterings under nät i ett virtuellt nätverk. Undernät separeras logiskt av regler för nätverks säkerhets grupper som tillämpas på enskilda undernät för att begränsa trafiken mellan undernät till endast det som krävs för system-och hanterings funktioner.

Se konfigurationen för [nätverks säkerhets grupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) som distribueras med den här lösningen. Organisationer kan konfigurera nätverks säkerhets grupper genom att redigera filen ovan med hjälp av [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som en guide.

Varje undernät har en särskild nätverks säkerhets grupp:
- 1 nätverks säkerhets grupp för Application Gateway (LBNSG)
- 1 nätverks säkerhets grupp för skydds-värd (MGTNSG)
- 1 nätverks säkerhets grupp för SQL-servrar och moln vittne (SQLNSG)
- 1 nätverks säkerhets grupp för webb nivå (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. 

För skyddade data som överförs från kundägda nätverk använder arkitekturen Internet eller ExpressRoute med en VPN Gateway som kon figurer ATS med IPSEC.

Dessutom sker alla transaktioner till Azure via Azures hanterings Portal via HTTPS som använder TLS 1,2.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom kryptering, databas granskning och andra åtgärder.

**Azure Storage**: För att möta krypterade data med rest-krav använder alla [Azure Storage](https://azure.microsoft.com/services/storage/) [kryptering för lagringstjänst](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper till att skydda och skydda data som stöd för organisationens säkerhets åtaganden och krav på efterlevnad som definieras av den australiska regeringens ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

**SQL Server**: SQL Server-instansen använder följande säkerhets åtgärder för databasen:
-   [SQL Server granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) spårar databas händelser och skriver dem till gransknings loggar.
-   [Transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte omfattas av obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [Krypterade kolumner](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) ser till att känsliga data aldrig visas som klartext i databas systemet. När du har aktiverat data kryptering kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterade data.
- [Dynamisk data maskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) begränsar känslig data exponering genom att maskera data till icke-privilegierade användare eller program. Dynamisk data maskning kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Detta bidrar till att minska åtkomsten så att känsliga data inte avslutar databasen via obehörig åtkomst. **Kunderna ansvarar för att justera inställningarna för dynamisk datamaskering för att följa deras databas schema.**

### <a name="identity-management"></a>Identitetshantering
Kunder kan använda lokala Active Directory federerade tjänster för att federera med [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), som är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integrera lokala kataloger med Azure Active Directory. Alla användare i den här lösningen kräver Azure Active Directory-konton. Med Federations inloggning kan användarna logga in till Azure Active Directory och autentisera till Azure-resurser med lokala autentiseringsuppgifter.

Dessutom kan följande Azure Active Directory funktioner hantera åtkomst till data i Azure-miljön:
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Med [rollbaserad åtkomst kontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella sårbarheter som påverkar en&#39;organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder som är&#39;relaterade till en organisations identiteter. undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

**Azure Multi-Factor Authentication**: För att skydda identiteter bör Multi-Factor Authentication implementeras. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) är en lättanvänd, skalbar och tillförlitlig lösning som ger en andra autentiseringsmetod för att skydda användare. Azure Multi-Factor Authentication använder kraften i molnet och integreras med lokala Active Directory och anpassade program. Det här skyddet har utökats till verksamhets kritiska scenarier med hög volym.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda och komma åt sådana data:

- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en säkerhetsmodul för maskin varu säkerhet som skyddas 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med rollbaserad åtkomst kontroll.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS virtuella datorer disk krypterings nycklar och hemligheter.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för Virtual Machines ger real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara, med konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera eller kör på skyddade virtuella datorer.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom ger Azure Security Center åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.

Den här referens arkitekturen använder dessutom sårbarhets [bedömningen](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Azure Security Center. När en partner agent har kon figurer ATS (t. ex. Qualys) rapporteras sårbarhets data till partnerns hanterings plattform. Partnerns hanterings plattform ger i sin tur sårbarhets-och hälso övervaknings data till Azure Security Center, så att kunderna snabbt kan identifiera sårbara virtuella datorer.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhets problem med en Azure Application Gateway med en brand vägg för webbaserade program konfigurerad, och OWASP-ruleset har Aktiver ATS. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) -avlastning
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brand vägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (skydds läge)
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0 ruleset
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälso avsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.

### <a name="business-continuity"></a>Verksamhetskontinuitet
**Hög tillgänglighet**: Lösningen distribuerar alla virtuella datorer i en tillgänglighets [uppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighets uppsättningar säkerställer att de virtuella datorerna distribueras över flera isolerade maskin varu kluster för att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

**Recovery Services valv**: [Recovery Services Vault](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av Azure-Virtual Machines i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställnings tider.

**Moln vittne**: [Moln vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av ett klusterkvorum i Windows Server 2016 som utnyttjar Azure som medlings punkt. Moln vittnet, precis som alla andra kvorumresurser, får en röst och kan delta i kvorumkonfigurationen, men använder allmänt tillgängliga Azure-Blob Storage. Detta eliminerar extra underhålls kostnader för virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp, som låter alla data analyseras tillsammans oavsett originalkälla. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Den Active Directory hälso kontroll lösningen utvärderar risker och hälso tillstånd för Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [SQL-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum och ger kunderna en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från Azure SQL Server. Med Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) -lösningen kan kunder enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): I Azure Network Watcher finns verktyg för att övervaka, diagnostisera, visa mått samt aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk.  Samväldet entiteter bör implementera Network Watcher flödes loggar för NSG: er och Virtual Machines. Dessa loggar bör lagras i ett dedikerat lagrings konto som bara säkerhets loggar lagras i och åtkomst till lagrings kontot bör skyddas med rollbaserad åtkomst kontroll.

## <a name="threat-model"></a>Hot modell
Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/au-protected-iaaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![IaaS webb program för au-skyddad hot modell](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS-webbprogram för au-skyddat hot modell diagram")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den här dokumentationen för efterlevnad skapas av Microsoft baserat på plattformar och tjänster som är tillgängliga från Microsoft. På grund av de många olika kund distributioner ger den här dokumentationen en generaliserad metod för en lösning som bara finns i Azure-miljön. Kunder kan identifiera och använda alternativa produkter och tjänster baserat på egna drift miljöer och affärs resultat. Kunder som väljer att använda lokala resurser måste hantera säkerheten och åtgärderna för de lokala resurserna. Den dokumenterade lösningen kan anpassas av kunder för att lösa sina särskilda lokala och säkerhets krav.

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – au-skyddad kund ansvar](https://aka.ms/au-protected-crm) visas alla säkerhets kontroller som krävs av au-skyddade. Den här matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure – au-skyddad IaaS Web Application implementation](https://aka.ms/au-protected-iaaswa-cim) finns information om vilka au-skyddade kontroller som hanteras av IaaS-webbappens arkitektur, inklusive detaljerade beskrivningar av Hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
För sekretessbelagd information måste en säker IPSec-tunnel konfigureras för att upprätta en anslutning till resurserna som distribueras som en del av den här IaaS-webbprogramets referens arkitektur. Genom att konfigurera en IPSec-VPN på ett korrekt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker IPSec VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk. Den här anslutningen kan ske via Internet och gör det möjligt för kunder att på ett säkert sätt ange "tunnel" information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. 

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft ett privat anslutnings alternativ. Azure dataExpressRoutes är en dedikerad länk mellan Azure och en lokal plats eller en Exchange-värd leverantör och betraktas som ett privat nätverk. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet och lägre fördröjning än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

För att skydda klassificerade data, oavsett om de använder Internet eller Azure-ExpressRoute, måste kunderna konfigurera IPSec VPN genom att använda följande inställningar:

• Kundens VPN-initierare måste konfigureras för en SA-livstid som inte är längre än 14400 sekunder.
• Kundens VPN-initierare måste inaktivera IKEv1 aggressivt läge.
• Kundens VPN-initierare måste konfigurera PFS (Perfect Forward Secrecy).
• Kundens VPN-initierare måste konfigurera användningen av HMAC-SHA256 eller större.

Konfigurations alternativ för VPN-enheter och IPSec/IKE-parametrar är [tillgängliga](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) för granskning.

### <a name="azure-active-directory-setup"></a>Azure Active Directory installation
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är viktigt för att hantera distributionen och tillhandahålla åtkomst till personal som interagerar med miljön. En befintlig Windows Server-Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Dessutom tillåter [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) kunder att konfigurera Federation med lokala [Active Directory Federation Services (AD FS)]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) och Azure Active Directory. Med Federations inloggning kan kunderna göra det möjligt för användarna att logga in på Azure Active Directory-baserade tjänster med sina lokala lösen ord och utan att behöva ange sina lösen ord på nytt i företags nätverket. Genom att använda Federations alternativet med Active Directory Federation Services (AD FS) kan du distribuera en ny installation av Active Directory Federation Services (AD FS), eller så kan du ange en befintlig installation i en Windows Server 2012 R2-grupp.

För att förhindra att klassificerade data synkroniseras till Azure Active Directory kan kunderna begränsa de attribut som replikeras till Azure Active Directory genom att använda följande inställningar i Azure Active Directory Connect:
- [Aktivera filtrering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Inaktivera synkronisering av lösen ords-hash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Inaktivera tillbakaskrivning av lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Inaktivera tillbakaskrivning av enhet](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Lämna standardinställningarna för [förhindra oavsiktliga borttagningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) och [automatisk uppgradering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Ansvarsfriskrivning
- Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
- Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
- Kunder får kopiera och använda det här dokumentet för intern referens.
- Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
- Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
- Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
