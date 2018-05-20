---
title: Azure-säkerhet och efterlevnad modell - IaaS webbprogram för BNPR
description: Azure-säkerhet och efterlevnad modell - IaaS webbprogram för BNPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure-säkerhet och efterlevnad modell - IaaS webbprogram för BNPR

## <a name="overview"></a>Översikt
Allmänna Data Protection förordning (BNPR) innehåller många krav om att samla in, lagra och använda personliga information, inklusive hur organisationer identifiera och skydda personliga data, hantera krav, identifiera och rapportera personliga data överträdelser och utbilda sekretess personal och andra anställda. BNPR får enskilda användare större kontroll över deras personliga data och inför många nya skyldigheter på organisationer som samlar in, hantera och analysera personliga data. BNPR inför nya regler för organisationer som erbjuder varor och tjänster till personer i Europeiska unionen (EU), eller att samla in och analysera data som är knutna till Europa boende. BNPR gäller oavsett var en organisation finns.

Microsoft har utvecklat Azure med branschledande säkerhetsåtgärder och sekretesspolicy skydda data i molnet, inklusive kategorier av personuppgifter som identifieras av BNPR. Microsofts [avtalsvillkor](http://aka.ms/Online-Services-Terms) genomför Microsoft kraven för processorer.

Den här Azure-säkerhet och efterlevnad utkast vägledning för att distribuera en infrastruktur som en tjänst (IaaS) miljö som är lämplig för ett enkelt Internet-riktade webbprogram. Den här lösningen visar sätt som kunder kan använda för att uppfylla specifika krav på säkerhet och efterlevnad av BNPR och fungerar som grund att skapa och konfigurera egna IaaS web application-lösningar i Azure-kunder. Kunder kan använda denna Referensarkitektur och följa Microsofts [fyra steg](https://aka.ms/gdprebook) i transporten BNPR kompatibiliteten:
1. Identifiera: Identifiera vilka personuppgifter finns och var den finns.
2. Hantera: Styr hur personuppgifter används och komma åt.
3. Skydda: Upprätta säkerhetsåtgärder för att förebygga, upptäcka och åtgärda säkerhetsproblem och dataintrång.
4. Rapport: Hålla krävs dokumentationen och hantera databegäranden om och bryta mot meddelanden.

Den här referensen för arkitekturen, associerade Implementeringsguide och hotmodell är avsedda att fungera som en grund för kunder att anpassa sig till deras specifika krav och ska inte användas som – i en produktionsmiljö. Observera följande:
- Arkitekturen ger en baslinje för att hjälpa kunderna distribuerar arbetsbelastningar till Azure på ett sätt som BNPR-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömningar av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen distribuerar en Referensarkitektur för ett IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivå, Active Directory-infrastruktur, Programgateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data som har konfigurerats i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och principer för Active Directory-grupper används för att framtvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå. En värd för hantering av skyddsmiljön innehåller en säker anslutning för administratörer att komma åt distribuerade resurser. **Azure rekommenderar konfigurera VPN eller ExpressRoute-anslutningen för hanterings- och import till referens arkitektur undernät.**

![IaaS Web Flersvalsstart för BNPR referens Arkitekturdiagram](images/gdpr-iaaswa-architecture.png?raw=true "IaaS Web Flersvalsstart för BNPR referens Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

- Azure Virtual Machines
    - (1) hantering/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server-2017 på Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL klusternoder
    - (1) web/IIS
- Azure Virtual Network
    - ((1) /16 virtuella nätverk
    - (5) /24 undernät
    - DNS-inställningarna är inställda på båda domänkontrollanterna
- Azure Load Balancer
- Azure Application Gateway
    - (1) Brandvägg Programgateway aktiverad
        - brandväggsläge: förebyggande
        - Regelsamlingen: OWASP 3.0
        - lyssnare: port 443
- Azure Storage
    - (7) Geo-redundant lagringskonton
- Molnet vittne
- Recovery Services-valv
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Arkitektur för distribution
I följande avsnitt beskrivs distribution och implementeringen elementen.

**Skyddsmiljö värden**: skyddsmiljö värden är den enda post som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö värden ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärråtkomst trafiken från offentliga IP-adresser på listan över säkra. Källan för trafiken måste definieras i nätverkssäkerhetsgrupp (NSG) för att tillåta fjärråtkomst fjärrskrivbord (RDP)-trafik.

Den här lösningen skapar en virtuell dator som värd domänanslutna skyddsmiljö med följande konfigurationer:
-   [Tillägg för program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägget](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med hjälp av Azure Key Vault
-   En [automatisk avstängning princip](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som
-   [Windows Defender autentiseringsuppgifter Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) aktiverats så att autentiseringsuppgifter och andra hemligheter som körs i en skyddad miljö som är isolerad från operativsystem

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: den här lösningen distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och hantering av undernät i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på individuella undernät för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner.

Se konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera nätverkssäkerhetsgrupper genom att redigera filen ovan med [denna dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som en vägledning.

Varje undernät har en dedikerad nätverkssäkerhetsgrupp (NSG):
- 1 NSG för Programgateway (LBNSG)
- 1 NSG för skyddsmiljön värden (MGTNSG)
- 1 NSG för primära och sekundära domänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar och molnet vittne (SQLNSG)
- 1 NSG för webbnivå (WEBNSG)

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. Dessutom kan uppstå alla transaktioner till Azure Storage via Azure-portalen via HTTPS.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila genom flera åtgärder, inklusive kryptering och granskning för databasen.

**Azure Storage**: att uppfylla krypterade data på övriga krav, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda och skydda personuppgifter för att stödja organisationens säkerhetsåtaganden och efterföljandekrav som definieras av BNPR.

**Azure Disk Encryption**: Azure Disk Encryption används som krypteras Windows IaaS virtuella diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för Operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

**SQL Server**: SQL Server-instans använder följande säkerhetsåtgärder för databasen:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. TDE ger försäkran som lagras i personliga data inte har omfattas obehörig åtkomst.
-   [Regler i brandväggen](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Alltid krypterade kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känslig personlig information aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering bara klientprogram eller programservrar med tillgång till nycklarna kan komma åt oformaterad text data.
- Den [utökade egenskaper](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funktionen kan användas för att avbryta bearbetningen av registrerade, som tillåter användare att lägga till anpassade egenskaper databasobjekt och tagga data som ”utgått” för att stödja programlogiken till att förhindra att den bearbetning av associerade personliga data.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan du definiera principer för att begränsa åtkomsten till data att avbryta bearbetningen.
- [SQL Database dynamiska Data maskering (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga personliga data genom att kombinera data till icke-privilegierade användare eller program. DDM kan automatiskt identifiera potentiellt känsliga data och föreslår lämpliga masker som ska användas. Detta hjälper med identifiering av personliga uppgifter som är kvalificerade för BNPR skydd och för att minska åtkomst så att det inte finns databasen via obehörig åtkomst. **Obs: Kunder behöver justera DDM för att uppfylla sina databasschemat.**

### <a name="identity-management"></a>Identitetshantering
Följande tekniker innehåller funktioner för att hantera åtkomst till personliga data i Azure-miljön:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för den här lösningen skapas i AAD, inklusive användare som ansluter till SQL Server.
-   Tillämpningen utförs autentiseringen med AAD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering AAD för att autentisera programmet till SQL Server. Mer information finns i så här [skydda känsliga data i SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter att bevilja endast åtkomstnivå som användare måste utföra sitt arbete. Istället för att ge varje obegränsad användarbehörigheter för Azure-resurser, kan administratörer endast vissa åtgärder för att komma åt personliga data. Prenumerationen åtkomst begränsas till administratör för prenumerationen.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till vissa resurser.  Administratörer kan använda AAD Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att tillämpa på begäran, just-in-time administrativ åtkomst vid behov.
- [AAD identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder kan lösas.
- En distribuerad IaaS Active Directory-instans innehåller Identitetshantering på OS-nivå för distribuerade IaaS virtuella datorer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälpa kunderna skydda personliga data och åtkomst till sådana data:
- Avancerade åtkomstprinciper konfigureras på grundval av behov.
- Key Vault åtkomstprinciper definieras med minsta behörighet till nycklar och hemligheter.
- Alla nycklar och hemligheter i Nyckelvalvet ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade av särskilda maskinvarusäkerhetsmoduler (HSM). Nyckeltypen är en HSM-skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med RBAC.
- Diagnostik-loggarna för Key Vault aktiveras med en kvarhållningsperiod på minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklarna är begränsade till de som krävs.
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS virtuell disk-krypteringsnycklar och hemligheter.

**Hantering av korrigering**: Windows-datorer som distribueras som en del av denna Referensarkitektur är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen innehåller också OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) tjänsten via vilken uppdaterade distributioner kan skapas för korrigering virtuella datorer när de behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installeras eller köras på den skyddade virtuella datorer.

**Säkerhetsaviseringar**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) ger kunder möjlighet att övervaka trafik, samla in loggar och analysera datakällor för hot. Dessutom öppnar Azure Security Center befintliga konfigurationen av Azure-tjänster att ge konfiguration och tjänsterekommendationer för att förbättra säkerhet och skydda personliga data. Azure Security Center innehåller en [hot intelligence-rapporten](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) för alla upptäckta hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Programgateway**: arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg för webbaserade program (Brandvägg) och OWASP ruleset aktiverat. Ytterligare funktioner är:

- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS version 1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Brandvägg läge)
- [Förebyggande läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 RuleSet-metod
- Aktivera [diagnostikloggning](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: lösningen distribuerar alla virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningar se till att de virtuella datorerna är fördelade på flera kluster isolerade maskinvara för att förbättra tillgänglighet. Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% SLA för Azure.

**Recovery Services-ventilen**: den [Återställningstjänstvalvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) inrymmer säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i Azure i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, aktivera snabbare återställning.

**Molnet vittne**: [moln vittne](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom punkten. Vittne molnet, precis som alla andra kvorumvittne hämtar en omröstning och kan delta i kvorum beräkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Granskning och loggning

OMS innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://azure.microsoft.com/services/log-analytics/) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.
- **Aktivitetsloggar**: [aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att bestämma en åtgärd initieraren tid på förekomst och status.
- **Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) inkluderar alla loggar som orsakat av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, Key Vault granskningsloggar och Programgateway loggar för åtkomst och brandväggen.
- **Arkivering av loggen**: alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar till bevarande organisation-specifika krav. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom ingår följande OMS-lösningar som en del av denna arkitektur:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

## <a name="threat-model"></a>Hotmodell

Flödesdiagram för data (DFD) för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/gdprIaaSdfd) eller finns nedan. Den här modellen hjälper kunder att förstå punkterna i risken i infrastrukturen för system när ändringar görs.

![IaaS Web Flersvalsstart för BNPR hotmodell](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS Web Flersvalsstart för BNPR hotmodell")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad

Den [Azure-säkerhet och efterlevnad modell - BNPR kunden ansvar matrisen](https://aka.ms/gdprCRM) visar domänkontrollant och processor ansvar för alla BNPR artiklar. Observera att för Azure-tjänster, en kund är vanligen kontrollanten och Microsoft fungerar som processorn.

Den [Azure-säkerhet och efterlevnad modell - BNPR IaaS Web Application implementering Matrix](https://aka.ms/gdprIaaSweb) innehåller information om vilka BNPR artiklar hanteras av webbprogram för IaaS-arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad artikel.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN- och ExpressRoute
En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för IaaS-webbprogram. Kunder kan genom på lämpligt sätt att skapa en VPN eller ExpressRoute, lägga till ett lager av dataskydd under överföring.

Genom att implementera en säker VPN-tunnel med Azure, kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure Virtual Network. Den här anslutningen sker via Internet och ger kunder möjlighet att på ett säkert sätt ”tunnel” information i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Den [IPSec-tunnelläge](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) används i det här alternativet som en krypteringsmekanism för.

Eftersom trafiken i VPN-tunnel passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutning. Azure ExpressRoute är en fast WAN länken mellan Azure och en lokal plats eller en värdleverantör för Exchange. Som det inte går att ExpressRoute anslutningar över Internet, erbjuder dessa anslutningar mer tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över Internet. Dessutom eftersom det här är en direkt anslutning av kundens telekommunikation providern data överföras inte via Internet och därför visas inte för den.

Bästa praxis för att implementera en säker hybrid-nätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.  
- Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.  
- Kunderna får kopiera och använda det här dokumentet som intern referens.  
- Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.  
- Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
- Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
