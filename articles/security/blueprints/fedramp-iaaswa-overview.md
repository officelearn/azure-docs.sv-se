---
title: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för FedRAMP
description: Azure-säkerhet och efterlevnad skissen - IaaS-webbprogram för FedRAMP
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
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586142"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure Blueprint för säkerhet och efterlevnad: IaaS-webbprogram för FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) är ett program för amerikanska regeringen som ger en standardiserad metod för säkerhetsbedömningar, auktorisering och kontinuerlig övervakning för molnprodukter och tjänster. Den här Azure-säkerhet och efterlevnad skissen Automation ger vägledning för distribution av en FedRAMP-kompatibla infrastruktur som en tjänst (IaaS)-miljö som är lämplig för ett enkelt webbprogram mot Internet. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som en grund för kunder att bygga och Konfigurera sina egna lösningar på Azure. Lösningen implementerar en deluppsättning av kontroller från FedRAMP High-baslinje, baserat på SP NIST 800-53. Mer information om FedRAMP krav och den här lösningen finns i den [efterlevnad dokumentation](#compliance-documentation).
> [!NOTE]
> Den här lösningen distribueras till Azure Government.

Den här Azure-säkerhet och efterlevnad skissen Automation distribuerar automatiskt en IaaS web Referensarkitektur för program med förkonfigurerade säkerhetskontroller för att hjälpa kunder att uppnå efterlevnad med FedRAMP krav. Lösningen består av Azure Resource Manager-mallar och PowerShell-skript som hjälper resource distribution och konfiguration.

Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö. Det räcker inte att distribuera ett program i den här miljön utan ändringar att helt uppfylla kraven för FedRAMP High-baslinje. Observera följande:
- Den här arkitekturen ger en baslinje för att hjälpa kunder som använder Azure på ett sätt som FedRAMP-kompatibla.
- Kunderna ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömning av alla lösningar som skapats med den här arkitekturen kraven kan variera beroende på specifika för varje kund-implementering.

Titta på den här för en snabb överblick över hur den här lösningen fungerar [video](https://aka.ms/fedrampblueprintvideo) hon förklarar och visar dess distribution.

Klicka på [här](https://aka.ms/fedrampblueprintrepo) anvisningar för distribution.

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter
Den här lösningen används en Referensarkitektur för en IaaS-webbprogram med en SQL Server-serverdel. Arkitekturen innehåller en webbnivå, datanivån, Active Directory-infrastruktur, Application Gateway och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data är konfigurerade i en Tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och Active Directory-grupprinciper används för att tillämpa säkerhets- och konfigurationer på driftsystemnivå. En skyddsmiljö-värd ger en säker anslutning för administratörer att få åtkomst till distribuerade resurser. **Azure rekommenderar att du konfigurerar en VPN- eller Azure ExpressRoute-anslutning för hantering och import till undernätet för referens-arkitektur.**

![IaaS-webbprogram för FedRAMP-referens för Arkitekturdiagram](images/fedramp-iaaswa-architecture.png?raw=true "IaaS-webbprogram för FedRAMP-referens för Arkitekturdiagram")

Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Virtual Machines
    - (1) skyddsmiljö-värd (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2017 på Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Tillgänglighetsuppsättningar
    - (1) active Directory-domänkontrollanter
    - (1) SQL-klusternoder
    - (1) Web/IIS
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
- Azure Cloud Witness
- Recovery Services-valv
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (loggar)

## <a name="deployment-architecture"></a>Distributionsarkitektur för

I följande avsnitt finns element för utveckling och implementering.

**Skyddsmiljö-värd**: Skyddsmiljö-värd är den enda posten som tillhandahåller en säker anslutning för administratörer att få åtkomst till distribuerade resurser. Skyddade värdens NSG tillåter anslutningar enbart på TCP-port 3389 för RDP. Kunder kan konfigurera ytterligare skyddsmiljö-värd för att uppfylla kraven för organisation av systemhärdning.

### <a name="virtual-network"></a>Virtuellt nätverk
Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

**Nätverkssäkerhetsgrupper**: Den här lösningen används resurser i en arkitektur med ett separat undernät, databas-undernät, Active Directory-undernät och hanteringsundernätet i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på de enskilda undernäten att begränsa trafik mellan undernät för att endast som krävs för system och hanteringsfunktioner.

Finns på konfigurationen för [nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Kunder kan konfigurera nätverkssäkerhetsgrupper genom att redigera filen ovan med [den här dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som vägledning.

Varje undernät har en dedikerad nätverkssäkerhetsgrupp (NSG):
- 1 NSG för Application Gateway (LBNSG)
- 1 NSG för skyddsmiljö-värd (MGTNSG)
- 1 NSG för primära och sekundära domänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar (SQLNSG)
- 1 NSG för Webbnivån (WEBNSG)

**Undernät**: Varje undernät är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar vilande data med hjälp av flera åtgärder för kryptering.

**Azure Storage**: För att uppfylla kraven för datakryptering för data i vila, alla storage-konton används [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server är konfigurerad för att använda [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), som utför i realtid kryptering och dekryptering av data och loggfiler att skydda information i vila. TDE ger garantier att lagras data inte har omfattas av obehörig åtkomst.

Kunder kan också konfigurera följande säkerhetsåtgärder för SQL Server:
-   [AD-autentisering och auktorisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) gör det möjligt för Identitetshantering för databasanvändare och andra Microsoft-tjänster på en central plats.
-   [SQL-databasgranskning](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) spårar databasen händelser och skriver dem till en granskningslogg i ett Azure storage-konto.
-   [Brandväggsregler](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) förhindrar all åtkomst till databasservrar tills rätt behörigheter beviljas. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) gör det möjligt att identifiera och bemöta potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar för misstänkta databasaktiviteter, potentiella svagheter, SQL-inmatningsattacker och avvikande databasåtkomst mönster.
-   [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) se till att känsliga data aldrig visas som oformaterad text i databassystemet. När du har aktiverat kryptering av data, endast klientprogram eller app-servrar med åtkomst till nycklarna kan komma åt data i klartext.
-   [SQL Database dynamisk datamaskning](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kan göras efter referensarkitekturen distribuerar. Kunder kommer att behöva justera dynamisk datamaskning inställningar för att uppfylla sina databasschemat.

**Azure Disk Encryption**: Azure Disk Encryption används för att krypterade Windows IaaS VM-diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar BitLocker-funktion i Windows för att kryptera volymer OS och datadiskar. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklar.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker hanteringsfunktioner identitet i Azure-miljön:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst.
- Autentisering till ett kund-distribuerade program kan utföras med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserade åtkomsthantering för Azure. Prenumerationsåtkomst är begränsad till administratör för prenumerationen och åtkomst till resurser kan begränsas baserat på användarrollen.
- En distribuerad IaaS Active Directory-instans innehåller Identitetshantering på operativsystemnivå för distribuerade IaaS-datorer.

### <a name="security"></a>Säkerhet
**Hantering av hemligheter**: Lösningen använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Azure Key Vault hjälper till att hantera IaaS VM diskkrypteringsnycklar och hemligheter för denna Referensarkitektur.

**Uppdateringshantering**: Windows-datorer som distribueras av den här Azure-säkerhet och efterlevnad skissen Automation är konfigurerade som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen används också Azure Automation-lösningen genom vilka distributioner kan skapas för att distribuera uppdateringar till Windows-servrar när det behövs.

**Skydd mot skadlig kod**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer ger realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

**Application Gateway**: Arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg för webbaserade program (WAF) och OWASP-regeluppsättning aktiverat. Ytterligare funktioner är följande:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS v1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF-läge)
- [Förhindringsläge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 regeluppsättning

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse, uppfyller 99,95% serviceavtalet för Azure. Lösningen har distribuerats alla webbnivån och data nivå virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningarna ser till att de virtuella datorerna distribueras över flera isolerade maskinvarukluster att förbättra tillgängligheten. Dessutom är den här lösningen används SQL Server-datorer i en Tillgänglighetsuppsättning som en [AlwaysOn-tillgänglighetsgrupp](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Always On availability group funktionen ger funktioner för hög tillgänglighet och katastrofåterställning.

**Recovery Services Vault**: Den [Recovery Services-valv](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) lagrar säkerhetskopierade data och skyddar alla konfigurationer av Azure-datorer i den här arkitekturen. Med ett Recovery Services-valv kan kunder återställa filer och mappar från en IaaS-VM utan att återställa hela VM, att aktivera återställningen går snabbare.

**Molnet vittne**: [Molnet vittne](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) är en typ av kvorumvittne för redundanskluster i Windows Server 2016 som utnyttjar Azure som skiljedom. Molnvittnet, precis som alla andra kvorumvittnen, får en röst och kan delta i kvorumberäkningar, men den använder standard allmänt tillgängliga Azure Blob Storage. Detta eliminerar pålägget extra underhåll av virtuella datorer som finns i ett offentligt moln.

### <a name="logging-and-auditing"></a>Loggning och granskning

Azure Monitor-loggar innehåller utförlig loggning av system- och användaraktivitet samt filsystemets hälsa. Den [Azure Monitor loggar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lösningen samlar in och analyserar data som genereras av resurser i Azure och lokala miljöer.

- **Aktivitetsloggar:**  [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger information om åtgärder som utförts på resurser i en prenumeration. Aktivitetsloggar kan hjälpa dig att fastställa en åtgärd initierare för förekomst och status.
- **Diagnostikloggar:**  [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som genereras av varje resurs. Dessa loggar är Windows-händelsesystemloggar, Azure storage-loggar, granskningsloggar för Key Vault och åtkomst och brandväggen loggar i Application Gateway.
- **Logga arkivering:**  Alla diagnostikloggar skriva till en central och krypterade Azure storage-konto för arkivering. Kvarhållning konfigureras av användaren, upp till 730 dagar att uppfylla kraven för specifika kvarhållning. Dessa loggar ansluta till Azure Monitor-loggar för bearbetning, lagring och -instrumentpanelsrapportering.

Dessutom installeras följande övervakningslösningar som en del av den här arkitekturen. Observera att det är kundens ansvar att konfigurera dessa lösningar för att anpassas till FedRAMP säkerhetskontroller:
-   [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Kontroll av Active Directory hälsotillstånd lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och ger en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Program mot skadlig kod rapporterar status för skadlig kod, hot och skydd.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation-lösningen lagrar, kör och hanterar runbooks.
-   [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Instrumentpanelen för säkerhet och granskning ger en övergripande inblick i säkerhetstillståndet hos resurser genom att tillhandahålla mått på säkerhetsdomäner anmärkningsvärda problem, identifieringar, hotintelligens och vanliga säkerhetsfrågor.
-   [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL-hälsokontroll lösningen utvärderar risker och hälsotillstånd i server-miljöer med regelbundna intervall och tillhandahåller kunder med en prioriterad lista över rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
-   [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Lösningen för uppdateringshantering kan kundhantering av säkerhetsuppdateringar för operativsystemet, inklusive statusen för tillgängliga uppdateringar och processen för att installera nödvändiga uppdateringar.
-   [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Lösningen Agenthälsa rapporterar hur många agenter distribueras och deras geografisk fördelning, samt hur många agenter som inte svarar och antalet agenter som skickar driftdata.
-   [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics-lösningen hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
-   [Ändringsspårning](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Lösningen ändringsspårning kan kunder enkelt kan identifiera ändringar i miljön.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att organisationer kan granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i kunders Azure-resurser.

## <a name="threat-model"></a>Hotmodell
Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/fedrampWAdfd) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![IaaS-webbprogram för FedRAMP hotmodell](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS-webbprogram för FedRAMP hotmodell")

## <a name="compliance-documentation"></a>Dokumentation om efterlevnad

Den [Azure säkerhet och efterlevnad skissen - FedRAMP hög kundens ansvar matrisen](https://aka.ms/blueprinthighcrm) visar en lista över alla säkerhetskontroller som krävs av FedRAMP High-baslinje. Matrisen anger om implementeringen av varje kontroll ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - FedRAMP IaaS Web Application hög kontroll implementering Matrix](https://aka.ms/blueprintwacim) visar en lista över alla säkerhetskontroller som krävs av FedRAMP High-baslinje. Matrisen innehåller information som kontroller omfattas av IaaS webbprogramarkitektur, inklusive detaljerade beskrivningar av hur implementeringen uppfyller kraven för varje skyddad kontroll.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här Azure-säkerhet och efterlevnad skissen Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänsten för att distribuera resurser i Azure. Distribution av detaljerade instruktioner finns [här](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Den här lösningen distribueras till Azure Government.

#### <a name="quickstart"></a>Snabbstart
1. Klona eller ladda ned [detta](https://aka.ms/fedrampblueprintrepo) GitHub-lagringsplatsen till den lokala arbetsstationen.

2. Kör PowerShell-skript före: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klicka på knappen nedan, logga in på Azure portal, anger du de obligatoriska parametrarna för ARM-mallen och klicka på **köp**.

    [![Distribuera till Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
