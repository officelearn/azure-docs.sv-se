---
title: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för GDPR
description: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: f5f04fdaa12c716b21bafcc0abccc719428fe791
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242083"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för GDPR

## <a name="overview"></a>Översikt
Den allmänna Dataskyddsförordningen GDPR (Data Protection) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga dataöverträdelser och träna sekretess personal och andra anställda. GDPR ger enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera eller analysera personliga data. Dataskyddsförordningen inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU) eller att samla in och analysera data som EU invånare. Dataskyddsförordningen gäller oavsett var det finns en organisation.

Microsoft har utformat Azure med branschledande säkerhetsåtgärder och sekretesspolicyer för att skydda data i molnet, inklusive kategorierna av personuppgifter som identifieras av GDPR. Microsofts [avtalsvillkor](https://aka.ms/Online-Services-Terms) genomför Microsoft till kraven i processorer.

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för att distribuera en infrastruktur som en tjänst (IaaS)-miljö som är lämplig för ett enkelt webbprogram mot Internet. Den här lösningen visar sätt där kunder kan uppfylla specifika krav på säkerhet och efterlevnad av Dataskyddsförordningen och fungerar som en grund för kunder att skapa och konfigurera sina egna program webblösningar IaaS i Azure. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i sin resa till GDPR-efterlevnad:
1. Identifiera: Identifiera vilka personliga data finns och var den finns.
2. Hantera: Styr hur personlig data används och komma åt.
3. Skydda: Upprätta säkerhetskontroller för att förhindra, upptäcka och svara på säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och tränga in meddelanden.

Den här referensarkitekturen och associerade Implementeringsguide hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunder som distribuerar arbetsbelastningar till Azure på ett sätt som GDPR-kompatibel.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen används en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Application Gateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data är konfigurerade i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och Active Directory-grupprinciper används för att tillämpa säkerhets- och konfigurationer på driftsystemnivå. En skyddsmiljö-värd för hantering ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. **Azure rekommenderar att du konfigurerar en VPN eller ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![IaaS Web Applicaiton för GDPR-referens för Arkitekturdiagram](images/gdpr-iaaswa-architecture.png?raw=true "IaaS Web Applicaiton för Arkitekturdiagram för GDPR-referens")

Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Virtual Machines
    - (1) management/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL-klusternoder
    - (1) web/IIS
- Azure Virtual Network
    - ((1) /16 virtuella nätverk
    - (5) /24 undernät
    - DNS-inställningar är inställda på båda domänkontrollanterna
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF-Programgateway aktiverat
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP 3.0
        - Lyssnare: port 443
- Azure Storage
    - (7) Geo-redundant storage-konton
- Molnvittne
- Recovery Services-valv
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Log Analytics
- Azure Security Center

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Skyddsmiljö-värd**: skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen (NSG) för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Log Analytics-tillägget](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverad så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från vilket operativsystem som körs

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: den här lösningen används resurser i en arkitektur med ett separat undernät, databas-undernät, Active Directory-undernät och hanteringsundernätet i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på de enskilda undernäten att begränsa trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns i konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera nätverkssäkerhet grupper genom att redigera filen ovan med [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en dedikerad nätverkssäkerhetsgrupp (NSG):
- 1 NSG för Application Gateway (LBNSG)
- 1 NSG för skyddsmiljö-värd (MGTNSG)
- 1 NSG för primära och sekundära domänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar och Molnvittne (SQLNSG)
- 1 NSG för webbnivån (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Dessutom kan inträffa alla transaktioner till Azure Storage via Azure-portalen via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med flera åtgärder, inklusive kryptering och database-granskning.

**Azure Storage**: att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda personuppgifter som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av GDPR.

**Azure Disk Encryption**: Azure Disk Encryption används som krypteras Windows IaaS VM-diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**SQL Server**: SQL Server-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   SQL-databas är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger garantier att lagras personliga data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga personliga data aldrig ska visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- Den [Extended Properties](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funktionen kan användas för att avbryta bearbetningen av registrerade, eftersom den låter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att den bearbetningen av associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att upphöra att bearbetning.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att maskera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med identifiering av personliga data som är kvalificerade för GDPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder måste justerar DDM inställningarna för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för den här lösningen skapas i AAD, inklusive användare med åtkomst till SQL Server.
-   Det utförs autentiseringen till programmet med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera program till SQL Server. Läs mer om hur du [skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt personliga data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) gör det möjligt för kunder att minimera antalet användare som har åtkomst till vissa resurser.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar organisationens identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.
- En distribuerad IaaS Active Directory-instans innehåller Identitetshantering på operativsystemnivå för distribuerade IaaS-datorer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddade 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av RBAC.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS VM diskkrypteringsnycklar och hemligheter.

**Uppdateringshantering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänst genom vilken uppdaterade distributioner kan skapas för patch virtuella datorer när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd som hjälper att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom kommer Azure Security Center åt befintliga konfiguration av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Application Gateway**: arkitekturen minskar risken för säkerhetsproblem med en Programgateway med Brandvägg för webbaserade program (WAF) och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF-läge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: lösningen distribueras alla virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure.

**Recovery Services-valv**: den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

**Molnet vittne**: [Molnvittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Log Analytics tillhandahåller omfattande loggning av system- och användaraktivitet, samt filsystemets hälsa. Den [Log Analytics](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway.
- **Arkivera loggen**: alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning. Dessa loggar kan ansluta till Azure Log Analytics för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom kan ingår följande övervakningslösningar som en del av den här arkitekturen:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Health kontroll av Active Directory lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): med Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): The säkerhet och granskning instrumentpanelen innehåller en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL-hälsa och kontrollera lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): uppdateringshanteringslösningen kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): The agenthälsolösningen rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics-lösning som hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Change Tracking solution gör att kunder enkelt kan identifiera ändringar i miljön.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram (DFD) för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/gdprIaaSdfd) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![IaaS Web Applicaiton för GDPR hotmodell](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS Web Applicaiton för GDPR hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen - GDPR kundens ansvar matrisen](https://aka.ms/gdprCRM) visar en lista över styrenhet och processor ansvar för alla GDPR-artiklar. Observera att för Azure-tjänster en kund är vanligtvis domänkontrollanten och Microsoft fungerar som processorn.

Den [Azure säkerhet och efterlevnad skissen - GDPR IaaS Web Application implementering Matrix](https://aka.ms/gdprIaaSweb) innehåller information som på vilka GDPR artiklar täcks av arkitekturen IaaS-webbprogram, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.

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
