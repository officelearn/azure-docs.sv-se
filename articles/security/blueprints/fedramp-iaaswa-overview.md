---
title: Azure-säkerhet och efterlevnad modell - IaaS webbprogram för FedRAMP
description: Azure-säkerhet och efterlevnad modell - IaaS webbprogram för FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 27346d8e2133ea61056817fb48050a4e5c8c3c97
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726490"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure-säkerhet och efterlevnad utkast: IaaS webbprogram för FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk och auktorisering hanteringsprogram (FedRAMP)](https://www.fedramp.gov) är ett US government hela program som tillhandahåller en standardiserad metod för utvärdering av säkerheten, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure-säkerhet och efterlevnad utkast Automation vägledning för distribution av en FedRAMP-kompatibel infrastruktur som en tjänst (IaaS) miljö som är lämplig för ett enkelt Internet-riktade webbprogram. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund för kunder att bygga och Konfigurera sina egna lösningar på Azure. Lösningen implementerar en deluppsättning av kontroller från FedRAMP hög baslinje, baserat på NIST SP 800 53. Mer information om FedRAMP kraven och den här lösningen finns i [kompatibilitet dokumentationen](#compliance-documentation).
> [!NOTE]
> Den här lösningen distribueras till Azure Government.

Den här Azure-säkerhet och efterlevnad utkast Automation distribuerar automatiskt en IaaS web application Referensarkitektur med förkonfigurerade säkerhetsåtgärder för att hjälpa kunderna att uppnå kompatibilitet med FedRAMP krav. Lösningen består av mallar för Azure Resource Manager och PowerShell-skript som hjälper resource distribution och konfiguration.

Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är inte tillräcklig för helt uppfyller kraven för FedRAMP hög baslinjen. Observera följande:
- Den här arkitekturen ger en baslinje för att hjälpa kunderna att använda Azure på ett sätt som FedRAMP-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömning av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering.

För en snabb överblick över hur den här lösningen fungerar, titta på den här [video](https://aka.ms/fedrampblueprintvideo) förklarar och visar dess distribution.

Klicka på [här](https://aka.ms/fedrampblueprintrepo) anvisningar för distribution.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen distribuerar en Referensarkitektur för ett IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivå, Active Directory-infrastruktur, Programgateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data som har konfigurerats i en Tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och principer för Active Directory-grupper används för att framtvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå. En skyddsmiljö-värd innehåller en säker anslutning för administratörer att komma åt distribuerade resurser. **Azure rekommenderar att konfigurera en VPN- eller Azure ExpressRoute-anslutning för hanterings- och import till referens arkitektur undernät.**

![IaaS-webbprogram för FedRAMP referens Arkitekturdiagram](images/fedramp-iaaswa-architecture.png?raw=true "IaaS webbprogram för FedRAMP referens Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

- Azure Virtual Machines
    - (1) skyddsmiljö värden (Windows Server 2016 Datacenter)
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
- Azure-molnet vittne
- Recovery Services-valv
- Azure Key Vault
- Azure Active Directory (AD Azure)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>Arkitektur för distribution

I följande avsnitt beskrivs element för utveckling och implementering.

**Skyddsmiljö värden**: skyddsmiljö värden är den enda post som innehåller en säker anslutning för administratörer att komma åt distribuerade resurser. Skyddsmiljön värdens NSG tillåter anslutningar enbart på TCP-port 3389 för RDP. Kunder kan konfigurera ytterligare skyddsmiljö värden för att uppfylla organisationens krav för systemhärdning.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: den här lösningen distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och hantering av undernät i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på individuella undernät för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner.

Kontrollera konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Kunder kan konfigurera nätverkssäkerhetsgrupper genom att redigera filen ovan med [denna dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som en vägledning.

Varje undernät har en dedikerad nätverkssäkerhetsgrupp (NSG):
- 1 NSG för Programgateway (LBNSG)
- 1 NSG för skyddsmiljön värden (MGTNSG)
- 1 NSG för primära och sekundära domänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar (SQLNSG)
- 1 NSG för Webbnivå (WEBNSG)

**Undernät**: varje undernät som är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med hjälp av flera åtgärder för kryptering.

**Azure Storage**: för att uppfylla kraven för datakryptering för data i vila kan använda alla lagringskonton [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), som utför realtid kryptering och dekryptering av data och loggfilen filer att skydda information i vila. TDE ger försäkran om att lagrade data inte har omfattas obehörig åtkomst.

Kunder kan också konfigurera följande säkerhetsåtgärder för SQL Server:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering av databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL database auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing-get-started) spårar databashändelser och skriver dem till en granskningslogg logga in ett Azure storage-konto-databas.
-   [Regler i brandväggen](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) förhindra all åtkomst till databasservrar tills rätt behörigheter. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL-Hotidentifiering](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection-get-started) aktiverar identifiering och svar på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella säkerhetsrisker, SQL injection attacker och avvikande databaser mönster.
-   [Always Encrypted kolumner](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault) känsliga data aldrig ska visas i klartext i databassystemet. När du har aktiverat datakryptering endast klientprogram eller appservrar med tillgång till nycklarna kan komma åt oformaterad text data.
-   [SQL-databas dynamisk datamaskning](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter den referens för arkitekturen distribuerar. Kunderna måste du justera inställningar för att uppfylla sina databasschemat för dynamisk datamaskering.

**Azure Disk Encryption**: Azure Disk Encryption används som krypteras Windows IaaS virtuella diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för Operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker hanteringsfunktioner identitet i Azure-miljön:
- [Azure Active Directory (AD Azure)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten.
- Autentisering till ett kunden distribuerade webbprogram kan utföras med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserad åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumerationen och åtkomst till resurser kan begränsas baserat på användarrollen.
- En distribuerad IaaS Active Directory-instans innehåller Identitetshantering på OS-nivå för distribuerade IaaS virtuella datorer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Azure Key Vault hjälper dig hantera IaaS virtuell disk-krypteringsnycklar och hemligheter för denna Referensarkitektur.

**Hantering av korrigering**: Windows-datorer som distribueras med den här Azure-säkerhet och efterlevnad utkast Automation är som standard konfigurerad att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen också distribuerar Azure Automation-lösningen som distributioner kan skapas för att distribuera korrigeringsfiler till Windows-servrar när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar När känd skadlig eller oönskad programvara försöker installeras eller köras på den skyddade virtuella datorer.

**Programgateway**: arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg för webbaserade program (Brandvägg) och OWASP ruleset aktiverat. Ytterligare funktioner är:

- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS version 1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Brandvägg läge)
- [Förebyggande läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 RuleSet-metod

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% SLA för Azure. Lösningen distribuerar alla webbnivå och data tjänstnivån virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningar se till att de virtuella datorerna är fördelade på flera kluster isolerade maskinvara för att förbättra tillgänglighet. Dessutom lösningen distribueras de virtuella datorerna i SQL Server i en Tillgänglighetsuppsättning som en [AlwaysOn-tillgänglighetsgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Always On availability group funktionen ger för hög tillgänglighet och katastrofåterställning funktioner.

**Recovery Services-ventilen**: den [Återställningstjänstvalvet](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) inrymmer säkerhetskopierade data och skyddar alla konfigurationer av virtuella datorer i Azure i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, aktivera snabbare återställning.

**Molnet vittne**: [moln vittne](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom punkten. Vittne molnet, precis som alla andra kvorumvittne hämtar en omröstning och kan delta i kvorum beräkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Granskning och loggning

OMS innehåller utförlig loggning av system- och användaraktivitet samt systemhälsa. OMS [logganalys](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.

- **Aktivitetsloggar:**[aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att bestämma en åtgärd initieraren tid på förekomst och status.
- **Diagnostikloggar:**[diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som orsakat av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure storage-loggar, Key Vault granskningsloggar och Programgateway loggar för åtkomst och brandväggen.
- **Arkivering av logg:** alla diagnostikloggar skriva till en central och krypterad Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar till bevarande organisation-specifika krav. Dessa loggar ansluta till Azure Log Analytics för bearbetning, lagring och rapportering av instrumentpanelen.

Dessutom installeras följande OMS-lösningar som en del av denna arkitektur. Observera att det är kundens ansvar för att konfigurera dessa lösningar för att justera FedRAMP säkerhetsåtgärder:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): I Active Directory Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Program mot skadlig kod Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): det program mot skadlig kod rapporter om status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): I Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granska](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): på säkerhet och granska instrumentpanelen ger en övergripande inblick i säkerhetsstatusen på resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotinformation och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): The SQL Health Check lösningen utvärderar risk och hälsotillståndet för miljöer med regelbundna intervall och ger kunder med en prioriterad lista med rekommendationer som är specifika för en distribuerad serverinfrastruktur.
-   [Uppdateringshantering](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): det uppdateringshantering lösningen kan kundhantering av operativsystemet, säkerhetsuppdateringar, inklusive statusen tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): den Agenthälsa lösningen rapporterar hur många agenter distribueras och deras geografiska spridning samt hur många agenter som inte svarar och antalet agenter som skickar användningsdata.
-   [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): den aktivitet Log Analytics-lösning som hjälper till med analys av Azure aktivitetsloggar över alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Change Tracking lösningen ger kunder möjlighet att enkelt identifiera ändringar i miljön.

**Azure-Monitor**
[Azure-Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) hjälper användarna att spåra prestanda, upprätthålla säkerheten och identifiera trender genom att aktivera organisationer att granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i kundernas Azure-resurser.

## <a name="threat-model"></a>Hotmodell
Flödesdiagram för data för denna Referensarkitektur är tillgänglig för [hämta](https://aka.ms/fedrampWAdfd) eller finns nedan. Den här modellen hjälper kunder att förstå punkterna i risken i infrastrukturen för system när ändringar görs.

![IaaS-webbprogram för FedRAMP hotmodell](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS Web Flersvalsstart för FedRAMP hotmodell")

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad

Den [Azure-säkerhet och efterlevnad modell - FedRAMP hög kunden ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen anger om genomförandet av varje kontroll ansvarar för Microsoft, kund, eller delas mellan två.

Den [Azure-säkerhet och efterlevnad modell - FedRAMP IaaS Web Application hög kontroll implementering Matrix](https://aka.ms/blueprintwacim) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen innehåller information som omfattas kontroller av IaaS web application arkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här Azure-säkerhet och efterlevnad utkast Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänsten för att distribuera resurser i Azure. Detaljerade stegvisa anvisningar finns [här](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Den här lösningen distribueras till Azure Government.

#### <a name="quickstart"></a>Snabbstart
1. Klona eller hämta [detta](https://aka.ms/fedrampblueprintrepo) GitHub-lagringsplatsen till din lokala arbetsstation.

2. Kör PowerShell-skript före distributionen: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klicka på knappen nedan, logga in på Azure-portalen, ange de obligatoriska parametrarna för ARM-mallen och klicka på **inköp**.

    [![Distribuera till Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
