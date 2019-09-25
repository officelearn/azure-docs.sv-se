---
title: Handlingsplan för säkerhet och efterlevnad i Azure-IaaS-webbprogram för NIST SP 800-171
description: Handlingsplan för säkerhet och efterlevnad i Azure-IaaS Web Application NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 83d368e419550f38c173a7a1dca42c84db7d542f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259842"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Handlingsplan för säkerhet och efterlevnad i Azure-IaaS-webbprogram för NIST SP 800-171

## <a name="overview"></a>Översikt
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller rikt linjer för att skydda sekretessbelagda oklassificerade uppgifter (Cui) som finns i icke-federala informations system och organisationer. NIST SP 800-171 fastställer 14 familjer av säkerhets krav för att skydda konfidentialiteten hos CUI.

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning för att hjälpa kunder att distribuera en webb program arkitektur i Azure som implementerar en delmängd av NIST SP 800-171-kontroller. Den här lösningen visar hur kunder kan uppfylla särskilda krav på säkerhet och efterlevnad. Den fungerar också som grund för kunderna att bygga och konfigurera egna webb program i Azure.

Den här referens arkitekturen, tillhör ande implementerings guide och hot modell är avsedda att fungera som grund för kunderna att anpassa sig efter deras särskilda krav. De bör inte användas som de är i en produktions miljö. Att distribuera den här arkitekturen utan modifiering är inte tillräckligt för att uppfylla kraven i NIST SP 800-171. Kunderna ansvarar för att utföra lämpliga utvärderingar av säkerhet och efterlevnad av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på vad som gäller för varje kunds implementering.

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter
Den här Handlingsplan för säkerhet och efterlevnad i Azure distribuerar en referens arkitektur för ett IaaS-webbprogram med en SQL Server Server del. Arkitekturen omfattar en webb nivå, datanivå, Active Directory infrastruktur, Azure Application Gateway och Azure Load Balancer. Virtuella datorer som distribueras till webben och data nivåer konfigureras i en tillgänglighets uppsättning. SQL Server instanser konfigureras i en tillgänglighets grupp som alltid är tillgängliga för hög tillgänglighet. De virtuella datorerna är domänanslutna. Active Directory grup principer tillämpar konfigurationer för säkerhet och efterlevnad på operativ system nivå.

Hela lösningen bygger på Azure Storage som kunderna konfigurerar från Azure Portal. Storage krypterar alla data med Kryptering för lagringstjänst för att bevara konfidentialiteten för data i vila. Geo-redundant lagring garanterar att en negativ händelse på kundens primära Data Center inte leder till förlust av data. En andra kopia lagras på en annan plats hundratals mil bort.

För ökad säkerhet hanteras alla resurser i lösningen som en resurs grupp via Azure Resource Manager. Azure Active Directory (RBAC-baserad åtkomst kontroll i Azure AD) används för att styra åtkomsten till distribuerade resurser och nycklar i Azure Key Vault. System hälsan övervakas via Azure Monitor. Kunderna konfigurerar båda övervaknings tjänsterna för att avbilda loggar. System hälsan visas på en enda instrument panel som är lätt att använda.

En skydds-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. *Microsoft rekommenderar att du konfigurerar en VPN-eller Azure ExpressRoute-anslutning för hantering och data import till referens arkitektur under nätet.*


![IaaS webb program för NIST SP 800-171-referens arkitektur diagram](images/nist171-iaaswa-architecture.png "IaaS webb program för NIST SP 800-171-referens arkitektur diagram")

Den här lösningen använder följande Azure-tjänster. Mer information finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Azure virtuella maskiner
    - (1) hantering/skydds (Windows Server 2016 Data Center)
    - (2) Active Directory domänkontrollant (Windows Server 2016 Data Center)
    - (2) SQL Server klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 nätverk
    - (5)/24 nätverk
    - (5) nätverks säkerhets grupper
- Tillgänglighetsuppsättningar
    - (1) Active Directory domänkontrollanter
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Application Gateway
    - (1) brand vägg för webbaserade program
        - Brand Väggs läge: skydd
        - Regel uppsättning: OWASP 3,0
        - Lyssnar port: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (loggar)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Moln vittne
- Recovery Services-valv

## <a name="deployment-architecture"></a>Distributions arkitektur
I följande avsnitt beskrivs distributions-och implementerings elementen.

**Skydds-värd**: Skydds-värden är den enda post adressen som användarna kan använda för att få åtkomst till de distribuerade resurserna i den här miljön. Skydds-värden ger en säker anslutning till distribuerade resurser genom att endast tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i nätverks säkerhets gruppen (NSG).

Den här lösningen skapar en virtuell dator som en domänansluten skydds-värd med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure-diagnostik-tillägg](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) att använda Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) som minskar användningen av VM-resurser när de inte används.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) är aktiverat så att autentiseringsuppgifter och andra hemligheter körs i en skyddad miljö som är isolerad från det operativ system som körs.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adress utrymme på 10.200.0.0/16.

**Nätverks säkerhets grupper**: Den här lösningen distribuerar resurser i en arkitektur med separata undernät för webb, databas, Active Directory och hantering i ett virtuellt nätverk. Undernät separeras logiskt av NSG-regler som tillämpas på enskilda undernät. Reglerna begränsar trafiken mellan undernät till det som krävs för system-och hanterings funktioner.

Se konfigurationen för [NSG: er](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) som distribueras med den här lösningen. Organisationer kan konfigurera NSG: er genom att redigera föregående fil med hjälp av [den här dokumentationen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) som en guide.

Varje undernät har en dedikerad NSG:
- En NSG för Application Gateway (LBNSG)
- En NSG för skydds-värden (MGTNSG)
- En NSG för primära domänkontrollanter och reservdomänkontrollanter (ADNSG)
- En NSG för SQL-servrar och moln vittne (SQLNSG)
- En NSG för webb nivå (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azures Data Center som standard. Dessutom sker alla transaktioner för lagring via Azure Portal via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom flera mått. Dessa åtgärder omfattar kryptering och databas granskning.

**Azure Storage**: För att uppfylla kraven för krypterade data i vila använder [](https://azure.microsoft.com/services/storage/) all lagring [kryptering för lagringstjänst](../../storage/common/storage-service-encryption.md). Den här funktionen hjälper till att skydda och skydda data i stöd för organisationens säkerhets åtaganden och krav på efterlevnad som definieras av NIST SP 800-171.

**Azure Disk Encryption**: Disk kryptering används för att kryptera Windows IaaS VM-diskar. [Disk kryptering](../azure-security-disk-encryption-overview.md) använder BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar. Lösningen är integrerad med Key Vault som hjälper dig att styra och hantera disk krypterings nycklarna.

**SQL Server**: SQL Server-instansen använder följande säkerhets åtgärder för databasen:
-   [SQL Server granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) spårar databas händelser och skriver dem till gransknings loggar.
-   [Transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler för att skydda information i vila. Transparent data kryptering garanterar att lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brand Väggs regler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databas servrar förrän rätt behörigheter har beviljats. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [Krypterade kolumner](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) ser till att känsliga data aldrig visas som oformaterad text i databas systemet. När data kryptering har Aktiver ATS kan endast klient program eller program servrar med åtkomst till nycklarna komma åt oformaterad text data.
- [Dynamisk data maskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) begränsar känslig data exponering genom att maskera data till användare eller program utan privilegier. Den kan automatiskt identifiera potentiellt känsliga data och föreslå lämpliga masker som ska användas. Dynamisk data maskning hjälper till att minska åtkomsten så att känsliga data inte avslutar databasen via obehörig åtkomst. *Kunderna ansvarar för att justera inställningarna för att följa deras databas schema.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure AD och omfattar de användare som har åtkomst till SQL Server-instansen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Mer information finns i så här [integrerar du program med Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan användas av administratörer för att definiera detaljerade åtkomst behörigheter som endast ger åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kan användas av kunder för att minimera antalet användare som har åtkomst till vissa resurser. Administratörer kan använda Azure AD Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter som påverkar en organisations identiteter. Den konfigurerar automatiserade svar på identifierade misstänkta åtgärder som rör en organisations identiteter. Den undersöker också misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.

### <a name="security"></a>Säkerhet
**Hemligheter, hantering**: Lösningen använder [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och-tjänster. Följande Key Vault funktioner hjälper kunder att skydda data:
- Avancerade åtkomst principer konfigureras på grund av behov.
- Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault har förfallo datum.
- Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en maskin varu säkerhetsmodul – skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas minst de behörigheter som krävs med RBAC.
- Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.
- Lösningen är integrerad med Key Vault för att hantera IaaS VM disk kryptering nycklar och hemligheter.

**Uppdaterings hantering**: Virtuella Windows-datorer som distribueras som en del av den här referens arkitekturen konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen omfattar även den [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer vid behov.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) för virtuella datorer tillhandahåller real tids skydds funktioner som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Kunder kan konfigurera aviseringar som genererar när kända skadlig eller oönskad program vara försöker installeras eller köras på skyddade virtuella datorer.

**Azure Security Center**: Med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Security Center också åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.

Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller misstänkt aktivitet äger rum. Kunder kan använda [anpassade aviserings regler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) för att definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.

Security Center innehåller prioriterade säkerhets aviseringar och incidenter. Security Center gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En [hot informations rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierat hot. Incident hanterings team kan använda rapporterna när de undersöker och reparerar hot.

Den här referens arkitekturen använder funktionen för [sårbarhets bedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Security Center. När den har kon figurer ATS rapporterar en partner agent (till exempel Qualys) sårbarhets data till partnerns hanterings plattform. Partnerns hanteringsplattform skickar i sin tur tillbaks sårbarhets- och hälsoövervakningsdata till Security Center. Kunder kan använda den här informationen för att snabbt identifiera sårbara virtuella datorer.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhets risker genom att använda en Programgateway med en brand vägg för webbaserade program konfigurerad och OWASP-regel uppsättningen aktive rad. Fler funktioner är:

- [Slut punkt till slut punkt – SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivera [SSL-avlastning](../../application-gateway/create-ssl-portal.md).
- Inaktivera [TLS v 1.0 och v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Brand vägg för webbaserade program](../../application-gateway/waf-overview.md) (skydds läge).
- [Skydds läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3,0-regel uppsättning.
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Anpassade hälso avsökningar](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och aviseringar. Security Center ger också ett ryktes system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Lösningen distribuerar alla virtuella datorer i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighets uppsättningar säkerställer att de virtuella datorerna distribueras över flera isolerade maskin varu kluster för att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse som uppfyller 99,95% Azure SLA.

**Recovery Services valv**: [Recovery Services Vault](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) skyddar säkerhets kopierings data och skyddar alla konfigurationer av Azure-Virtual Machines i den här arkitekturen. Med ett Recovery Services-valv kan kunderna återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn. Den här processen påskyndar återställnings tider.

**Moln vittne**: [Moln vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av ett kvorumlogg för redundanskluster i Windows Server 2016 som använder Azure som medlings punkt. Moln vittne, precis som andra kvorumresurser, får en röst och kan delta i kvorumkonfigurationen. Den använder den standardiserade, allmänt tillgängliga Azure Blob Storage. I den här ordningen elimineras extra underhålls kostnader för virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
- **Aktivitets loggar**: [Aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, lagrings loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar, för att uppfylla de särskilda kraven.

**Azure Monitor loggar**: Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in organiseras de i separata tabeller för varje datatyp inom Log Analytics arbets ytor. På så sätt kan alla data analyseras tillsammans, oavsett den ursprungliga källan. Security Center integreras med Azure Monitor-loggar. Kunder kan använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

Följande lösningar för Azure- [övervakning](../../monitoring/monitoring-solutions.md) ingår som en del av den här arkitekturen:
-   [Active Directory utvärdering](../../azure-monitor/insights/ad-assessment.md): Active Directory hälso kontrolls lösningen utvärderar hälso-och hälso tillståndet för Server miljöer med jämna mellanrum. Den innehåller en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [SQL-utvärdering](../../azure-monitor/insights/sql-assessment.md): I SQL Health Check-lösningen bedöms risken och hälsan i Server miljöer med jämna mellanrum. Den ger kunder en prioriterad lista med rekommendationer som är speciella för den distribuerade Server infrastrukturen.
- [Agenthälsa](../../monitoring/monitoring-solution-agenthealth.md): Agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution. Den rapporterar även hur många agenter som inte svarar och antalet agenter som skickar drift data.
-   [Aktivitetslogganalys](../../azure-monitor/platform/collect-activity-logs.md): Aktivitetslogganalys lösning hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar Runbooks. I den här lösningen kan Runbooks samla in loggar från SQL Server. Kunder kan använda Automation [ändringsspårning](../../automation/change-tracking.md) -lösningen för att enkelt identifiera ändringar i miljön.

**Azure Monitor**: [Övervakaren](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/nist171-iaaswa-tm) eller finns här. Den här modellen kan hjälpa kunderna att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![IaaS-webbprogram för NIST SP 800-171 hot modell](images/nist171-iaaswa-threat-model.png "IaaS-webbprogram för NIST SP 800-171 hot modell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-NIST SP 800-171-kund ansvar](https://aka.ms/nist171-crm) visas alla säkerhets kontroller som krävs av NIST SP 800-171. Den här matrisen anger om implementeringen av varje kontroll är ansvar för Microsoft, kunden eller delas mellan de två.

[Implementerings matrisen handlingsplan för säkerhet och efterlevnad i Azure-NIST sp 800-171 IaaS för webb program kontroll](https://aka.ms/nist171-iaaswa-cim) innehåller information om vilka NIST SP 800-171-kontroller som hanteras av arkitekturen för IaaS-webbprogram. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje kontroll som omfattas.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att på ett säkert sätt upprätta en anslutning till resurserna som distribuerats som en del av den här IaaS referens arkitektur för webb program. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen äger rum via Internet. Kunder kan använda den här anslutningen för att på ett säkert sätt ange "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypterings metod.

Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft ännu fler säkra anslutnings alternativ. ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. ExpressRoute-anslutningar ansluter direkt till kundens teleoperatörs leverantör. Det innebär att data inte färdas över Internet och inte exponeras för den. Dessa anslutningar ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar. 

Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det. 
- Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning. 
- Kunder får kopiera och använda det här dokumentet för intern referens. 
- Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader. 
- Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
- Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
