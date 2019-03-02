---
title: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för SP NIST 800-171
description: Azure-säkerhet och efterlevnad skissen - SP för IaaS Web Application NIST 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241331"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera en webbprogramarkitektur i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visas hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav. Det fungerar också som en grund för kunder att skapa och konfigurera sina egna webbprogram i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav. De bör inte användas som-är i en produktionsmiljö. Distribuera den här arkitekturen utan ändringar är tillräckligt för att uppfylla kraven för SP NIST 800-171 helt. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen. Kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här Azure Blueprint för säkerhet och efterlevnad distribuerar du en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Azure Application Gateway och Azure Load Balancer. Virtuella datorer (VM) som distribueras på nivåerna web och data är konfigurerade i en tillgänglighetsuppsättning. SQL Server-instanserna är konfigurerade i en Always On-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är ansluten till en domän. Active Directory-grupprinciper tillämpa säkerhets- och konfigurationer på driftsystemnivå.

Hela lösningen bygger på Azure Storage, som kunder konfigurera från Azure-portalen. Storage krypteras alla data med Storage Service Encryption för att upprätthålla sekretessen för data i vila. GEO-redundant lagring säkerställer att en incident på kundens primära Datacenter inte resultera i en förlust av data. En andra kopia lagras i en separat plats hundratals mil bort.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory (Azure AD)-rollbaserad åtkomstkontroll (RBAC) används för att styra åtkomst till distribuerade resurser och nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar. Platssystemets hälsostatus visas i en enda instrumentpanel som är lätt att använda.

En skyddsmiljö-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. *Microsoft rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.*


![IaaS-webbprogram för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-iaaswa-architecture.png "IaaS-webbprogram för Arkitekturdiagram för SP NIST 800-171-referens")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - ((1) /16 nätverk
    - (5) /24 nätverk
    - (5) nätverkssäkerhetsgrupper
- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssningsport: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (loggar)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Cloud Witness
- Recovery Services-valv

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som användarna kan använda för att få åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd är en säker anslutning till distribuerade resurser eftersom endast fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen (NSG) för att tillåta trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägg för Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Key Vault.
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av Virtuella datorresurser som.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiveras så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerat från vilket operativsystem som körs.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: Den här lösningen används resurser i en arkitektur med separata undernät för webb-, databas, Active Directory och hantering i ett virtuellt nätverk. Undernät är logiskt åtskilda av NSG-regler som tillämpas på de enskilda undernäten. Reglerna begränsar trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns i konfigurationen för [NSG: er](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera NSG: er genom att redigera den tidigare filen med hjälp av [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en dedikerad NSG:
- En NSG för Application Gateway (LBNSG)
- En NSG för skyddsmiljö-värd (MGTNSG)
- En NSG för primära och sekundära domänkontrollanter (ADNSG)
- En NSG för SQL-servrar och Molnvittne (SQLNSG)
- En NSG för webbnivån (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Dessutom kan inträffa alla transaktioner till Storage via Azure-portalen via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera mått. Dessa åtgärder omfattar kryptering och database-granskning.

**Azure Storage**: Att uppfylla krav för krypterade data i vila, alla [Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Den här funktionen kan du skydda och skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av SP NIST 800-171.

**Azure Disk Encryption**: Diskkryptering används för att krypterade Windows IaaS VM-diskar. [Diskkryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) använder BitLocker-funktion i Windows för att kryptera volymer för operativsystem och datadiskar. Lösningen är integrerad med Key Vault för att styra och hantera diskkrypteringsnycklar.

**SQL Server**: SQL Server-instansen använder följande säkerhetsåtgärder för databasen:
-   [Granskning av SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) spårar databashändelser och skriver dem om du vill granska loggar.
-   [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger assurance som lagrade data inte har varit föremål för obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [Krypterade kolumner](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När datakryptering är aktiverat, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt oformaterad text data.
- [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) begränsar exponering av känsliga data genom att maskera data till replikdata användare eller program. Det kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Dynamisk datamaskning hjälper till att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. *Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat.*

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) är Microsofts multitenant molnbaserade katalog- och identity management-tjänsten. Alla användare för den här lösningen skapas i Azure AD och innehåller de användare som har åtkomst till SQL Server-instansen.
-   Autentisering till programmet utförs med hjälp av Azure AD. Läs mer om hur du [integrera program med Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kan användas av administratörer för att definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) kan användas av kunder för att minimera antalet användare som har åtkomst till vissa resurser. Administratörer kan använda Azure AD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella sårbarheter som påverkar organisationens identiteter. Konfigurerar den automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter. Det kan också undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Key Vault kan du skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvara security-modul-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
- Lösningen är integrerad med Key Vault för att hantera IaaS VM diskkrypteringsnycklar och hemligheter.

**Uppdateringshantering**: Windows-datorer som distribuerats som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för att korrigera virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Kunder kan konfigurera aviseringar som genereras när känd skadlig eller oönskad programvara försöker installeras eller köras på skyddade virtuella datorer.

**Azure Security Center**: Med [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Security Center har även åtkomst till befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Security Center använder en mängd funktioner och meddela kunder om eventuella attacker som är inriktade på sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) som utlöses när ett hot eller misstänkt aktivitet äger rum. Kunder kan använda [anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Security Center tillhandahåller rangordnade säkerhetsaviseringar och incidenter. Security Center gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot. Incidenter team kan använda rapporterna när de undersöka och åtgärda hot.

Den här referensarkitekturen använder den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) funktioner i Security Center. När den har konfigurerats kan rapporterar en partner-agenten (till exempel Qualys) sårbarhetsdata till partnerns hanteringsplattform. Partnerns hanteringsplattform skickar i sin tur tillbaks sårbarhets- och hälsoövervakningsdata till Security Center. Kunder kan använda den här informationen för att snabbt identifiera sårbara VM: ar.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med en brandvägg för webbaserade program som konfigurerats och OWASP regeluppsättningen aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge).
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning.
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Anpassade hälsotillståndsavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Lösningen har distribuerats alla virtuella datorer i en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse som uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa en hel virtuell dator. Den här processen snabbare återställning gånger.

**Molnet vittne**: [Molnet vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för failover-kluster i Windows Server 2016 som använder Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar. Den använder standard allmänt tillgängliga Azure Blob storage. Det här eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Användare kan konfigurera kvarhållningsperioden, upp till 730 dagar att uppfylla deras specifika krav.

**Azure Monitor-loggar**: Dessa loggar konsolideras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp i Log Analytics-arbetsytor. På så sätt kan analyseras alla data tillsammans oavsett originalkälla. Security Center integrerar med Azure Monitor-loggar. Kunder kan använda Kusto-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Azure [övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall. Det ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Lösningen SQL-hälsokontroll bedömer risken och hälsotillståndet i server-miljöer med regelbundna intervall. Tjänsten ger kunderna med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning. Här rapporteras även hur många agenter är inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från SQL Server. Kunder kan använda Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösning lätt kan identifiera ändringar i miljön.

**Azure Monitor**: [Övervaka](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender. Organisationer kan använda den för att granska, skapa aviseringar och arkivera data. De kan också spåra API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-iaaswa-tm) eller hittar du här. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i infrastrukturen för system när de gör ändringar.

![IaaS-webbprogram för SP NIST 800-171 hotmodell](images/nist171-iaaswa-threat-model.png "IaaS-webbprogram för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 IaaS Web Application Control implementering Matrix](https://aka.ms/nist171-iaaswa-cim) innehåller information som på vilka SP NIST 800-171-kontroller med hjälp av IaaS-webbprogramarkitektur. Den innehåller detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för IaaS web application. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker över Internet. Kunder kan använda den här anslutningen till på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom VPN-tunneln trafik passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat anslutningsalternativ för ännu mer säker. ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. ExpressRoute-anslutningar ansluta direkt till kundens telekommunikation providern. Därför kan överföras inte via Internet data och visas inte. De här anslutningarna erbjuder mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar. 

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den. 
- Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar. 
- Kunderna får kopiera och använda det här dokumentet som intern referens. 
- Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration. 
- Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
- Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
