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
ms.openlocfilehash: f19478b29fd96cd2bd3ed5e0e994595d7b00edf3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391986"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för SP NIST 800-171

## <a name="overview"></a>Översikt
[NIST Specialpublicering 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) innehåller riktlinjer för att skydda den kontrollerad Oklassificerad information (CUI) som finns i nonfederal informationssystem och organisationer. SP NIST 800-171 upprättar 14 familjer av säkerhetskrav för att skydda konfidentialiteten för CUI.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att hjälpa kunder att distribuera en webbprogramarkitektur i Azure som implementerar en deluppsättning av SP NIST 800-171-kontroller. Den här lösningen visar hur kunder kan uppfylla specifika säkerhets- och efterlevnadskrav och fungerar som en grund för kunder att skapa och konfigurera sina egna webbprogram i Azure.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och bör inte användas som-är i en produktionsmiljö. Distribuera den här arkitekturen utan ändringar är tillräckligt för att uppfylla kraven för SP NIST 800-171 helt. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här Azure Blueprint för säkerhet och efterlevnad distribuerar du en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Application Gateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data är konfigurerade i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en Always On-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och Active Directory-grupprinciper används för att tillämpa säkerhets- och konfigurationer på driftsystemnivå.

Hela lösningen bygger på Azure Storage som kunder konfigurera från Azure-portalen. Azure Storage krypterar alla data med Storage Service Encryption för att upprätthålla sekretessen för data i vila. Geografiskt Redundant lagring säkerställer att en incident på kundens primära Datacenter inte leder till förlust av data, som en andra kopia kommer att lagras i en separat plats hundratals mil bort.

Alla resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser och nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel.

En skyddsmiljö-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. **Microsoft rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**


![IaaS-webbprogram för SP NIST 800-171 referens för Arkitekturdiagram](images/nist171-iaaswa-architecture.png "IaaS-webbprogram för Arkitekturdiagram för SP NIST 800-171-referens")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - ((1) /16 nätverk
    - (5) /24 nätverk
    - (5) nätverkssäkerhetsgrupper
- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL-klusternoder
    - (1) web/IIS
- Azure Application Gateway
    - (1) Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssningsport: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Molnvittne
- Recovery Services-valv

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: den här lösningen används resurser i en arkitektur med ett separat undernät, databas-undernät, Active Directory-undernät och hanteringsundernätet i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på de enskilda undernäten att begränsa trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns i konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera nätverkssäkerhet grupper genom att redigera filen ovan med [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en särskild grupp:
- 1 nätverkssäkerhetsgrupp för Application Gateway (LBNSG)
- 1 nätverkssäkerhetsgruppen för skyddsmiljö-värd (MGTNSG)
- 1 nätverkssäkerhetsgruppen för primära och sekundära domänkontrollanter (ADNSG)
- 1 nätverkssäkerhetsgruppen för SQL-servrar och molnet vittne (SQLNSG)
- 1 nätverkssäkerhetsgruppen för webbnivån (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Dessutom kan inträffa alla transaktioner till Azure Storage via Azure-portalen via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av SP NIST 800-171.

**Azure Disk Encryption**: Azure Disk Encryption används som krypteras Windows IaaS VM-diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att tillhandahålla volymkryptering för operativsystem och datadiskar. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**SQL Server**: SQL Server-instans använder följande säkerhetsåtgärder för databasen:
-   [Granskning av SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) spårar databashändelser och skriver dem om du vill granska loggar.
-   [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler för att skydda information i vila. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [Krypterade kolumner](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. **Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till SQL Server.
-   Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) gör det möjligt för kunder att minimera antalet användare som har åtkomst till vissa resurser.  Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarusäkerhetsmoduler nyckeltypen är en maskinvarusäkerhetsmodul skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS VM diskkrypteringsnycklar och hemligheter.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Azure Security Center**: med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

Dessutom är den här referensarkitekturen använder den [sårbarhetsbedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) i Azure Security Center. När du konfigurerat rapporterar en partner-agenten (t.ex. Qualys) sårbarhetsdata till partnerns hanteringsplattform. I sin tur innehåller partnerns hanteringsplattform säkerhetsproblem och hälsoövervakningsdata tillbaka till Azure Security Center, så att kunder kan snabbt identifiera sårbara virtuella datorer.

**Application Gateway**: arkitekturen minskar risken för säkerhetsproblem med en Programgateway med en brandvägg för webbaserade program som konfigurerats och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: lösningen distribueras alla virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

**Molnet vittne**: [Molnvittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Log Analytics**: de här loggarna konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När samlats in ordnas data i separata tabeller för varje datatyp i Operations Management Suite-arbetsytor, vilket gör att alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Log Analytics så att kunderna kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärderingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Server. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure resurser.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/nist171-iaaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![IaaS-webbprogram för SP NIST 800-171 hotmodell](images/nist171-iaaswa-threat-model.png "IaaS-webbprogram för SP NIST 800-171 hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 kundens ansvar matrisen](https://aka.ms/nist171-crm) visar en lista över alla säkerhetskontroller som krävs av SP NIST 800-171. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - SP NIST 800-171 IaaS Web Application Control implementering Matrix](https://aka.ms/nist171-iaaswa-cim) innehåller information som på vilka SP NIST 800-171-kontroller med hjälp av IaaS-webbprogramarkitektur inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för IaaS-webbprogram. Genom på lämpligt sätt att konfigurera en VPN eller ExpressRoute, kan kunderna lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och Azure Virtual Network. Den här anslutningen sker via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Den [IPSec-tunnelläge](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism.

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft alternativ för en annan ännu mer säker anslutning. Azure ExpressRoute är en dedikerad WAN länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.  
- Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.  
- Kunderna får kopiera och använda det här dokumentet som intern referens.  
- Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.  
- Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
- Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
