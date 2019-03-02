---
title: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för PCI DSS
description: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för PCI DSS
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 361d7683790128aafb11d6581c6e672b68ad893f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241603"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Azure Blueprint för säkerhet och efterlevnad: IaaS-webbprogram för PCI DSS

## <a name="overview"></a>Översikt

Innehåller riktlinjer för distribution av en kompatibel Payment Card Industry Data Security Standards (PCI DSS 3.2)-infrastruktur som en tjänst (IaaS)-miljö som är lämplig för insamling, lagring och hämtning av den här Azure Blueprint för säkerhet och efterlevnad kortinnehavare. Den visar en gemensam Referensarkitektur och visar hanterar kreditkortsdata (inklusive kort tal, förfallodatum och verifiering av data) i en miljö för säker, kompatibel och flera nivåer. Den här skissen illustrerar en slutpunkt till slutpunkt-lösning för att uppfylla behoven hos organisationer som vill ha en molnbaserad metod för att minska belastningen och kostnaden för distributionen.

Den här referensarkitekturen och Implementeringsguide för hotmodell ger en grund för kunder som uppfyller kraven för PCI DSS 3.2. Den här lösningen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett PCI DSS 3.2 kompatibla sätt. den här lösningen bör dock inte användas som-är i en produktionsmiljö, eftersom ytterligare konfiguration krävs.

För att uppnå PCI DSS-kompatibilitet krävs att en behörig Qualified Security Assessor (QSA) certifiera en lösning för produktion-kund. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

Den här lösningen används en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Application Gateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data är konfigurerade i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en Always On-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och Active Directory-grupprinciper används för att tillämpa säkerhets- och konfigurationer på driftsystemnivå. En skyddsmiljö-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. **Azure rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![IaaS-webbprogram för PCI DSS-referens för Arkitekturdiagram](images/pcidss-iaaswa-architecture.png "IaaS-webbprogram för Arkitekturdiagram för PCI DSS-referens")

Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL-klusternoder
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssningsport: 443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
    - (7) Geo-redundant storage-konton
- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1) /16 nätverk
    - (5) /24 nätverk
    - (5) Nätverkssäkerhetsgrupper
- Cloud Witness
- Recovery Services Vault

## <a name="deployment-architecture"></a>Distributionsarkitektur för

Följande avsnitt beskriver elementen distribution och implementering.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i Nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

### <a name="virtual-network"></a>Virtuellt nätverk

Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: Den här lösningen används resurser i en arkitektur med ett separat undernät, databas-undernät, Active Directory-undernät och hanteringsundernätet i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på de enskilda undernäten att begränsa trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns i konfigurationen för [Nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera Nätverkssäkerhetsgrupper genom att redigera filen ovan med [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en särskild grupp:
- 1 Nätverkssäkerhetsgrupp för Application Gateway (LBNSG)
- 1 nätverk säkerhetsgruppen för skyddsmiljö-värd (MGTNSG)
- 1 nätverkssäkerhet gruppen för primära och sekundära domänkontrollanter (ADNSG)
- 1 Nätverkssäkerhetsgruppen för SQL-servrar och Molnvittne (SQLNSG)
- 1 nätverk säkerhetsgruppen för webbnivån (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring

Azure krypterar all kommunikation till och från Azure-datacenter som standard. Dessutom kan inträffa alla transaktioner till Azure Storage via Azure-portalen via HTTPS.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

**Azure Storage**: Att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda kortinnehavare till stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av PCI DSS 3.2.

**Azure Disk Encryption**: Azure Disk Encryption används för att krypterade Windows IaaS VM-diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**SQL Server**: SQL Server-instansen använder följande säkerhetsåtgärder för databasen:
- [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering tillhandahåller säkra på att lagras kortinnehavare inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga kortinnehavare aldrig ska visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- Den [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funktionen kan användas för att avbryta bearbetningen av registrerade, eftersom den låter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att den bearbetning av associerade kortinnehavare.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga Kortinnehavarens data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper till att identifiera och minska åtkomst till kortinnehavare så att det inte finns databasen via obehörig åtkomst. Kunderna ansvarar för att ändra inställningarna för att uppfylla sina databasschemat för dynamisk datamaskning.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker ger funktioner för att hantera åtkomst till data i Azure-miljön:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsoft&#39;s flera innehavare molnbaserad katalog- och identity management-tjänsten. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare med åtkomst till Azure SQL-databasen.
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad behörighet för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisation&#39;s identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisation&#39;s identiteter , och undersöker misstänkta incidenter för att vidta lämpliga åtgärder du kan åtgärda detta.

### <a name="security"></a>Säkerhet

**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda och komma åt dessa data:

- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en HSM-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS VM diskkrypteringsnycklar och hemligheter.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Azure Application Gateway**: Arkitekturen minskar risken för säkerhetsproblem med Azure Application Gateway med en brandvägg för webbaserade program som konfigurerats och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Lösningen har distribuerats alla virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en virtuell IaaS-dator utan att återställa hela den virtuella datorn, att aktivera återställningen går snabbare.

**Molnet vittne**: [Molnet vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Azure Monitor-loggar**: Dessa loggar konsolideras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När samlats in ordnas data i separata tabeller för varje datatyp i Log Analytics-arbetsytor, vilket gör att alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Azure Monitor-loggar så att kunder kan använda Kusto-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Azure [övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Database. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure-resurser.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram (DFD) för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/pcidss-iaaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![IaaS-webbprogram för PCI DSS-referens för Arkitekturdiagram](images/pcidss-iaaswa-threat-model.png "IaaS-webbprogram för Arkitekturdiagram för PCI DSS-referens")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure Security Blueprint och efterlevnad – PCI DSS kundens ansvar matrisen](https://aka.ms/pcidss-crm) visar en lista över ansvar för alla krav för PCI DSS 3.2.

Den [Azure Security Blueprint och efterlevnad – PCI DSS IaaS Web Application implementering Matrix](https://aka.ms/pcidss-iaaswa-cim) innehåller information som på vilka PCI DSS 3.2 krav hanteras av arkitekturen för IaaS-webbprogram, inklusive detaljerad beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.

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
