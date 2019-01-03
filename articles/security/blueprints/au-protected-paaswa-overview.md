---
title: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för Australien SKYDDAS
description: Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för Australien SKYDDAS
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 3505d65b55807010904494079532fe5741e6df77
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601176"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure-säkerhet och efterlevnad skissen - PaaS webbprogram för Australien SKYDDAS

## <a name="overview"></a>Översikt

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning för distribution av en plattform som en tjänst (PaaS)-miljö som är lämplig för insamling, lagring och hämtning av AU-skyddade government data som är kompatibla med målen för den Australian Government Information Security manuell (ISM) skapas genom att Australian Signals Directorate (ASD). Den här skissen presenterar en gemensam Referensarkitektur och hjälper till att demonstrera hanterar känsliga government data i en miljö för säker, kompatibel och flera nivåer.

Den här referensarkitekturen och Implementeringsguide för hotmodell ger en grund för kunder att genomföra sina egna planerings- och system ackrediteringsprocesser att hjälpa kunder att distribuera arbetsbelastningar till Azure på ett sätt som ASD-kompatibel. Kunder kan välja att implementera en Azure VPN Gateway eller ExpressRoute att använda externa tjänster och integrera lokala resurser med Azure-resurser. Kunder måste ta hänsyn till säkerhetsriskerna med hjälp av lokala resurser. Ytterligare konfiguration krävs för att uppfylla alla krav, eftersom de kan variera beroende på specifika för varje kund-implementering.

För att uppnå ASD-kompatibilitet krävs att en Information Security registrerad bedömare granskar systemet. Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad utvärderingar av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen tillhandahåller en Referensarkitektur för ett PaaS-webbprogram med en Azure SQL Database-serverdel. Webbprogrammet är värd för en isolerad Azure App Service Environment, vilket är en privat, dedikerad miljö i en Azure-datacenter. Den miljö belastningsutjämning trafik för webbprogrammet över virtuella datorer som hanteras av Azure. Alla anslutningar för web application kräver TLS med lägst version 1.2. Den här arkitekturen omfattar också nätverkssäkerhetsgrupper, ett Application Gateway, Azure DNS och belastningsutjämnare.

Arkitekturen kan leverera en säker hybridmiljö som utökar ett lokalt nätverk till Azure, vilket gör att web-baserade arbetsbelastningar som kan nås på ett säkert sätt av företagets användare av en organisations privat lokalt nätverk eller från internet. Kunden är ansvariga för såväl som ansvarar för alla aspekter av säkerhet, åtgärder och efterlevnad för lokala lösningar.

Azure-resurser i den här lösningen kan ansluta till ett lokalt nätverk eller Datacenter delade miljö (t.ex. CDC i Canberra) via en IPSec VPN med en VPN-Gateway och via ExpressRoute. Beslutet att använda en VPN-anslutning ska göras med klassificeringen av överförda data och nätverkssökvägen i åtanke. Kunder som kör storskaliga, bör verksamhetskritiska arbetsbelastningar med stordatakrav en hybridnätverksarkitektur med ExpressRoute för privat nätverksanslutning till Azure-tjänster. Referera till den [vägledning och rekommendationer](#guidance-and-recommendations) avsnitt finns mer information om anslutningsmekanismerna till Azure.

Federation med Azure Active Directory ska användas för att användarna kan autentiseras med lokala autentiseringsuppgifter och få åtkomst till alla resurser i molnet med hjälp av en lokal Active Directory Federation Services-infrastruktur. Active Directory Federation Services kan ge förenklad, säkrad federation och enkel inloggning för funktioner för Identitetshantering för den här hybridmiljö. Referera till den [vägledning och rekommendationer](#guidance-and-recommendations) avsnitt för mer information om Azure Active Directory-installationen.

Lösningen använder Azure Storage-konton som kunder kan konfigurera för att använda kryptering av lagringstjänst för att upprätthålla sekretessen för data i vila. Azure lagrar tre kopior av data inom en kunds valda regionen för återhämtning. Azure-regioner har distribuerats i elastiska regionpar och geografiskt redundant lagring säkerställer att data replikeras till den andra regionen med samt tre kopior. Detta förhindrar att en incident på kundens primära platsen, vilket resulterar i förlust av data.

Alla Azure-resurser i den här lösningen som hanteras som en resursgrupp i Azure Resource Manager för ökad säkerhet. Azure Active Directory rollbaserad åtkomstkontroll används för att styra åtkomst till distribuerade resurser och nycklar i Azure Key Vault. Systemhälsa övervakas via Azure Security Center och Azure Monitor. Kunder konfigurera båda övervakningstjänster för att samla in loggar och visa filsystemets hälsa i en enda, enkelt tangentbordsgenvägarna instrumentpanel. Azure Application Gateway har konfigurerats som en brandvägg i förhindringsläge och inte tillåter trafik som inte är TLS version 1.2 eller senare. Lösningen använder Azure Application Service-miljö v2 för att isolera webbnivån i en miljö med flera organisationer.

![PaaS-webbprogram för AU-skyddade Referensarkitektur](images/au-protected-paaswa-architecture.png?raw=true "PaaS webbprogram för Arkitekturdiagram för AU-skyddade referens")

Den här lösningen använder följande Azure-tjänster. Mer information finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Application Gateway
    - Brandvägg för webbaserade program
        - Brandväggsläge: dataförlustskydd
        - Regeluppsättning: OWASP
        - Lyssningsport: 443
- Application Insights
- Azure Active Directory
- Azure Application Service-miljö v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 nätverk
    - (4) /24 nätverk
    - Nätverkssäkerhetsgrupper
- Nätverkssäkerhetsgrupper
- Recovery Services-valv
- Azure-webbapp

Den här skissen innehåller Azure-tjänster som inte har certifierats för användning i den skyddade klassificeringen av australiensiska Cyberhot Security Center (ACSC). Microsoft rekommenderar att kunder läsa publicerade säkerheten och granskningsrapporter relaterade till dessa Azure-tjänster och använda deras riskhanteringsramverket för att avgöra om Azure-tjänsten är lämplig för deras interna ackreditering och användning till den Skyddade klassificering.

## <a name="deployment-architecture"></a>Distributionsarkitektur för
Följande avsnitt beskriver elementen distribution och implementering.

**Med Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ger kunder möjlighet att arbeta med resurserna i lösningen som en grupp. Kunder kan distribuera, uppdatera eller ta bort alla resurser för lösningen i en enda, samordnad åtgärd. Kunder använder en mall för distributionen mallen kan användas för olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper kunder att hantera sina resurser efter distributionen.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som ger användare åtkomst till distribuerade resurser i den här miljön. Skyddsmiljö-värd ger en säker anslutning till distribuerade resurser genom att bara tillåta fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i nätverkssäkerhetsgruppen för att tillåta (RDP) trafik för fjärrskrivbordet.

Den här lösningen skapar en virtuell dator som en domänansluten skyddsmiljö-värd med följande konfigurationer:
-   [Tillägget mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics-tillägg](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) med Azure Key Vault
-   En [princip för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som

**App Service-miljö v2**: Den [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) är en funktion i App Service som innehåller en helt isolerad och dedikerad miljö för säker körning av App Service-program i hög skala.

App Service-miljöer är isolerade för att endast köra en enda kunds program och de distribueras alltid till ett virtuellt nätverk. Kunderna har detaljerad kontroll över både inkommande och utgående programnätverkstrafik och programmen kan upprätta säkra höghastighetsanslutningar över virtuella nätverk till lokala företagsresurser.

Användning av App Service-miljöer för den här arkitekturen tillåta följande kontroller/konfigurationer:

- App Service-miljöer måste konfigureras för att använda den fristående tjänstprenumerationen
    - Konfigurera olika App Service-miljöer för program på olika klassificeringar
- Värd i ett skyddat Azure virtuellt nätverk och Nätverkssäkerhetsregler
- App Service-miljöer som konfigurerats med ett självsignerat intern Azure load balancer-certifikat för HTTPS-kommunikation. Som bästa praxis rekommenderar Microsoft användningen av en betrodd certifikatutfärdare för förbättrad säkerhet.
- [Läge för intern belastningsutjämning](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (läge 3)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändra [TLS-chiffer](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrollen [inkommande trafik N/W portar](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Brandvägg för webbaserade program – begränsa data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Tillåt [Azure SQL Database-trafik](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Webbapp**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) ger kunder möjlighet att bygga och agera värd för webbprogram i programmeringsspråket helst utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps Services eller valfri Git-lagringsplats.

### <a name="virtual-network"></a>Virtual Network
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller listor för åtkomstkontroll som tillåter eller nekar trafik inom ett virtuellt nätverk. Nätverkssäkerhetsgrupper kan användas för att skydda trafik på ett undernät eller en enskild virtuell dator-nivå. Följande nätverkssäkerhetsgrupper finns:
- 1 nätverkssäkerhetsgruppen för Application Gateway
- 1 nätverkssäkerhetsgruppen för App Service Environment
- 1 nätverkssäkerhetsgruppen för Azure SQL Database
- 1 nätverkssäkerhetsgruppen för skyddsmiljö-värd

Var och en av nätverkssäkerhetsgrupperna har specifika portar och protokoll öppna så att lösningen fungerar på ett säkert sätt och korrekt. Dessutom kan är följande konfigurationer aktiverade för varje grupp:

  - [Diagnostiska loggar och händelser](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) är aktiverade och lagras i ett lagringskonto
  - Azure Log Analytics är ansluten till den [network security group diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Undernät**: Varje undernät är associerad med dess motsvarande grupp.

**Azure DNS**: Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Azure-infrastrukturen. Användare kan som värd för domäner i Azure, hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som för andra Azure-tjänster. Azure DNS stöder också privata DNS-domäner.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) gör att kunder kan skala programmen och skapa hög tillgänglighet för tjänster. Belastningsutjämnare har stöd för inkommande samt utgående scenarier, och ger med låg fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-program.

### <a name="data-in-transit"></a>Data under överföring
Azure krypterar all kommunikation till och från Azure-datacenter som standard. 

För skyddade data under överföring från kundägda nätverk använder arkitekturen Azure Internet eller ExpressRoute med en VPN-Gateway som konfigurerats med IPSEC.

Dessutom sker alla transaktioner till Azure via Azure-hanteringsportalen via HTTPS med TLS version 1.2.

### <a name="data-at-rest"></a>Vilande data
Arkitekturen skyddar data i vila med kryptering, database-granskning och andra åtgärder.

**Azure Storage**: Att uppfylla krypterade data vid krav på rest, alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption). Detta hjälper att skydda data som stöd för företagssäkerhet åtaganden och efterlevnadskrav som definieras av Australian Government ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer för datadiskar. Lösningen kan integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

**Azure SQL Database**: Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:
-   [Active Directory-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   Azure SQL-databasen är konfigurerad för att använda [transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), som utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler att skydda information på rest. Transparent datakryptering ger garantier att lagras data inte har omfattas av obehörig åtkomst.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster. SQL Threat Detection integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder att undersöka och åtgärda hot.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller programservrar med åtkomst till nycklarna kan komma åt data i klartext.
- [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) begränsar exponering av känsliga data genom att maskera data till icke-privilegierade användare eller program. Dynamisk datamaskning kan automatiskt identifiera potentiellt känsliga data och rekommenderar lämplig masker som ska användas. Detta hjälper med att minska åtkomst så att känsliga data inte lämna databasen via obehörig åtkomst. Kunder kommer att behöva justera dynamisk datamaskning inställningar för att uppfylla sina databasschemat.

### <a name="identity-management"></a>Identitetshantering
Kunder kan använda en lokal Active Directory Federation Services att federera med [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), vilket är Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integrerar lokala kataloger med Azure Active Directory. Alla användare i den här lösningen kräver Azure Active Directory-konton, inklusive användare med åtkomst till Azure SQL-databasen. Med federation inloggning, kan användare logga in på Azure Active Directory och autentisera till Azure-resurser med lokala autentiseringsuppgifter.

Dessutom är följande funktioner i Azure Active Directory hjälper dig att hantera åtkomst till data i Azure-miljön:
- Autentisering till programmet utförs med hjälp av Azure Active Directory. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering Azure Active Directory för att autentisera program till Azure SQL Database. Läs mer om hur du [skydda känsliga data i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Rollbaserad åtkomstkontroll i Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) gör att administratörer kan definiera detaljerade behörigheter som ska tilldelas endast mängden åtkomst som användare behöver att utföra sitt arbete. Istället för att ge varje obegränsad behörighet för Azure-resurser, kan administratörer tillåta enbart vissa åtgärder för att komma åt data. Prenumerationsåtkomst är begränsad till prenumerationsadministratören.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ger kunder möjlighet att minimera antalet användare som har åtkomst till viss information. Administratörer kan använda Azure Active Directory Privileged Identity Management för att upptäcka, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga på begäran just-in-time administrativ åtkomst vid behov.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar en organisationer identiteter, konfigurerar automatiska svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter att vidta lämpliga åtgärder du kan åtgärda detta.

**Azure Multi-Factor Authentication**: Multifaktorautentisering bör implementeras för att skydda identiteter. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) är en lättanvänd, skalbar och tillförlitlig lösning som ger ett annat sätt för att skydda användare. Azure Multi-Factor Authentication använder kraften i molnet och integreras med en lokal Active Directory och anpassade program. Det här skyddet utökas till verksamhetskritiska scenarion med stora volymer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande funktioner i Azure Key Vault hjälper kunder att skydda data:
- Avancerade åtkomstprinciper konfigureras på basis av behov.
- Åtkomstprinciper för Nyckelvalv har definierats med minsta obligatoriska behörigheter till nycklar och hemligheter.
- Alla nycklar och hemligheter i Key Vault ha ett förfallodatum.
- Alla nycklar i Key Vault är skyddade med specialiserade maskinvarubaserade säkerhetsmoduler. Nyckeltypen är en maskinvarusäkerhetsmodul skyddad 2048-bitars RSA-nyckel.
- Alla användare och identiteter beviljas lägsta behörigheten som krävs med hjälp av rollbaserad åtkomstkontroll.
- Diagnostikloggar för Key Vault har aktiverats med en period av minst 365 dagar.
- Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.

**Azure Security Center**: Med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), kunder kan centralt tillämpa och hantera säkerhetsprinciper i arbetsbelastningarna, begränsa exponeringen för hot, och identifiera och svara på attacker. Dessutom kommer Azure Security Center åt befintliga konfigurationer av Azure-tjänster att tillhandahålla konfiguration och rekommendationer för tjänster för att förbättra säkerhet och skydda data.

Azure Security Center använder en mängd funktioner för att meddela kunder om potentiella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning [fördefinierade säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), som utlöses när ett hot eller misstänkt aktivitet äger rum. [Anpassade aviseringsregler](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) i Azure Security Center kan kunderna definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.

Azure Security Center får rangordnade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En [hot intelligence-rapporten](https://docs.microsoft.com/azure/security-center/security-center-threat-report) genereras för varje identifierad hot mot hjälpa incidenter team undersöka och åtgärda hot.

**Application Gateway**: Arkitekturen minskar risken för säkerhetsproblem med en Programgateway med en brandvägg för webbaserade program och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [Slutpunkt till slutpunkt-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (förhindringsläge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning
- Aktivera [diagnostikloggning](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) tillhandahålla ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure-tjänster logga stor utsträckning system- och användaraktivitet samt systemhälsa:
- **Aktivitetsloggar**: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) omfattar alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure Storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway. Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning.

**Log Analytics**: Dessa loggar konsolideras i [Log Analytics](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och -instrumentpanelsrapportering. När samlats in ordnas data i separata tabeller för varje datatyp, vilket gör att alla data analyseras tillsammans oavsett originalkälla. Dessutom integrerar Azure Security Center med Log Analytics så att kunderna kan använda Log Analytics-frågor för att komma åt deras säkerhet händelsedata och kombinera dem med data från andra tjänster.

Följande Log Analytics [hanteringslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen:
-   [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) lagrar, kör och hanterar runbooks. I den här lösningen hjälper runbooks att samla in loggar från Azure SQL Database. Automation [ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking) lösningen gör det möjligt för kunder att lätt identifiera ändringar i miljön.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning API-anrop i sina Azure-resurser.

Azure Network Watcher: [Azure Network Watcher] 9 https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) innehåller verktyg för att övervaka, diagnostisera, visa mått, och aktivera eller inaktivera loggar för resurser i Azure-nätverk.  Samväldet entiteter bör implementera Network Watcher flödesloggar för NSG: er och virtuella datorer. Dessa loggar bör lagras på en dedikerad lagringskonto som endast säkerhetsloggar lagras i och åtkomst till storage-konto bör skyddas med rollen baserade åtkomstkontroller.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/au-protected-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![PaaS-webbprogram för AU-PROTECTED Hotmodell](images/au-protected-paaswa-threat-model.png?raw=true "PaaS webbprogram för AU-skyddade threat modelldiagram")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad
Den här dokumentationen för efterlevnad skapas av Microsoft baserat på plattformar och tjänster från Microsoft. På grund av det breda utbudet av kunddistributioner tillhandahåller den här dokumentationen en generaliserad för en lösning som endast finns i Azure-miljön. Kunder kan identifiera och använda andra produkter och tjänster baserat på sina egna miljöer och affärsresultat. Kunder som vill använda lokala resurser måste uppfylla säkerhets- och åtgärder för dessa lokala resurser. Dokumenterade lösningen kan anpassas av kunder att lösa sina specifika lokala och säkerhetskrav.

Den [Azure säkerhet och efterlevnad skissen - AU-PROTECTED kundens ansvar matrisen](https://aka.ms/au-protected-crm) visar en lista över alla säkerhetskontroller som krävs av AU-Prot. Den här matrisen information om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - AU-PROTECTED PaaS Web Application implementering Matrix](https://aka.ms/au-protected-paaswa-cim) innehåller information som AU-skyddade kontroller med hjälp av PaaS webbprogramarkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer
### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute

En säker tunnel med IPSec VPN måste konfigureras för att upprätta en anslutning till resurser som har distribuerats som en del av denna Referensarkitektur för IaaS web application för sekretessbelagda uppgifter. Kunder kan genom att korrekt ställa in ett IPSec VPN, lägga till ett lager säkerhet för data under överföring.

Genom att implementera en säker IPSec VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och ett Azure-nätverk. Den här anslutningen kan ske via Internet och kan kunderna på ett säkert sätt ”tunnel” informationen i en krypterad anslutning mellan kundens nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. 

Eftersom trafik i VPN-tunneln passerar via Internet med en plats-till-plats-VPN, erbjuder Microsoft ett alternativ för privat anslutning. Azure ExpressRoute är en dedikerad länk mellan Azure och en lokal plats eller en värdbaserade Exchange-provider och betraktas som ett privat nätverk. När ExpressRoute-anslutningar inte går via Internet, är dessa anslutningar mer tillförlitlighet, snabbare hastigheter och kortare svarstider än vanliga anslutningar via Internet. Dessutom eftersom detta är en direkt anslutning av kundens telekommunikation providern kan data följer inte med dig via Internet och därför visas inte för den.

Bästa praxis för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

För att skydda sekretessbelagda data, oavsett om du använder Internet eller Azure ExpressRoute, måste kunder konfigurera sina IPSec-VPN genom att använda följande inställningar:

• För kundens VPN-initieraren måste konfigureras för en SA-livstid inte är större än 14400 sekunder.
• För kundens VPN-initieraren måste inaktivera IKEv1 aggressivt läge.
• För kundens VPN-initieraren måste konfigurera Perfect Forward Secrecy.
• Kunden VPN-initierare måste konfigurera användningen av HMAC-SHA256 eller större.

Konfigurationsalternativen för VPN-enheter och IPSec / IKE-parametrar är [tillgängliga](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) för granskning.

### <a name="azure-active-directory-setup"></a>Konfigurationen av Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är nödvändig för att hantera distributionen och etablering åtkomst till personal som interagerar med miljön. En befintlig Windows Server Active Directory kan integreras med Azure Active Directory i [fyra klick](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Dessutom [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) låter kunder konfigurera federation med en lokal [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) och Azure Active Directory. Med federation inloggning, kan kunderna ge användare att logga in på Azure Active Directory-baserade tjänster med sina lokala lösenord och utan att behöva ange sina lösenord igen i företagsnätverket. Du kan distribuera en ny installation av Active Directory Federation Services med hjälp av alternativet federation med Active Directory Federation Services, eller du kan ange en befintlig installation i en Windows Server 2012 R2-servergrupp.

För att förhindra klassificerade data synkroniseras till Azure Active Directory, kan kunder begränsa de attribut som replikeras till Azure Active Directory genom att använda följande inställningar i Azure Active Directory Connect:

- [Aktivera filtrering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Inaktivera synkronisering av lösenordshash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Inaktivera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Inaktivera tillbakaskrivning av enhet](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Lämna standardinställningarna för [förhindra oavsiktliga borttagningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) och [automatisk uppgradering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
