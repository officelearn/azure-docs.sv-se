---
title: "Plan för Azure Automation - webbprogram för FedRAMP"
description: "Plan för Azure Automation - webbprogram för FedRAMP"
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
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d0521d68bab8bd0b7db53a512da6d37033abd85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation---web-applications-for-fedramp"></a>Plan för Azure Automation - webbprogram för FedRAMP

## <a name="overview"></a>Översikt

Den [Federal Risk och auktorisering hanteringsprogram (FedRAMP)](https://www.fedramp.gov), är ett US government hela program som tillhandahåller en standardiserad metod för utvärdering av säkerheten, auktorisering och kontinuerlig övervakning för molnet produkter och tjänster. Den här Azure utkast Automation - webbprogram för FedRAMP vägledning för distribution av en FedRAMP-kompatibel infrastruktur som en tjänst (IaaS) miljö som är lämplig för ett enkelt Internet-riktade webbprogram. Den här lösningen automatiserar distributionen och konfigurationen av Azure-resurser för en vanliga Referensarkitektur visar sätt där kunder kan uppfylla specifika krav för säkerhet och efterlevnad och fungerar som grund för kunder att bygga och Konfigurera sina egna lösningar på Azure. Lösningen implementerar en deluppsättning av kontroller från FedRAMP hög baslinje, baserat på NIST SP 800 53. Mer information om FedRAMP hög kraven och den här lösningen finns [FedRAMP höga krav - High-Level översikt](fedramp-controls-overview.md). ***Obs: Den här lösningen distribueras till Azure Government.***

Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är inte tillräcklig för helt uppfyller kraven för FedRAMP hög baslinjen. Observera följande:
- Den här arkitekturen ger en baslinje för att hjälpa kunderna att använda Azure på ett sätt som FedRAMP-kompatibel.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad bedömning av någon lösning som skapats med den här arkitekturen kraven kan variera baserat på egenskaperna för varje kund implementering. 

För en snabb överblick över hur den här lösningen fungerar, titta på den här [video](https://aka.ms/fedrampblueprintvideo) förklarar och visar dess distribution.

Klicka på [här](https://aka.ms/fedrampblueprintrepo) anvisningar för distribution.

## <a name="solution-components"></a>Lösningskomponenter

Den här Azure utkast Automation distribuerar automatiskt en IaaS web application Referensarkitektur med förkonfigurerade säkerhetsåtgärder för att hjälpa kunderna att uppnå kompatibilitet med FedRAMP krav. Lösningen består av mallar för Azure Resource Manager och PowerShell-skript som hjälper resource distribution och konfiguration. Tillhörande Azure-modell [kompatibilitet dokumentationen](#compliance-documentation) anges, som anger säkerhet kontrollen arv från Azure och distribuerade resurser och konfigurationer som överensstämmer med NIST SP 800 53 säkerhetsåtgärder, vilket göra det möjligt för organisationer snabb åtkomst till följs.

## <a name="architecture-diagram"></a>Arkitekturdiagram

Den här lösningen distribuerar en Referensarkitektur för ett IaaS-webbprogram med en serverdel för databasen. Arkitekturen innehåller en webbnivå, data-nivån, Active Directory-infrastruktur, Programgateway, och belastningsutjämnare. Virtuella datorer distribueras på nivåerna web och data som har konfigurerats i en tillgänglighetsuppsättning och SQL Server-instanserna är konfigurerade i en AlwaysOn-tillgänglighetsgrupp för hög tillgänglighet. Virtuella datorer är anslutna till en domän och principer för Active Directory-grupper används för att framtvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå. Management-jumpbox (skyddsmiljö host) innehåller en säker anslutning för administratörer att komma åt distribuerade resurser.

![ALT-text](images/fedramp-architectural-diagram.png?raw=true "IaaS web programmet utkast automation för FedRAMP-kompatibel miljöer")

Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

* **Virtuella Azure-datorer**
    - (1) hantering/skyddsmiljö (Windows Server 2016 Datacenter)
    - (2) active Directory-domänkontrollant (Windows Server 2016 Datacenter)
    - (2) SQL Server-klusternod (SQL Server 2016 på Windows Server 2012 R2)
    - (1) SQL Server-vittne (Windows Server 2016 Datacenter)
    - (2) web/IIS (Windows Server 2016 Datacenter)
* **Tillgänglighetsuppsättningar**
    - (1) active Directory-domänkontrollanter
    - (1) SQL klusternoderna och vittne
    - (1) web/IIS
* **Azure Virtual Network**
    - ((1) /16 virtuella nätverk
    - (5) /24 undernät
    - DNS-inställningarna är inställda på båda domänkontrollanterna
* **Azure Load Balancer**
    - (1) SQL belastningsutjämnare
* **Azure Programgateway**
    - (1) Brandvägg Programgateway aktiverad
      - Brandväggen läge: förebyggande
      - Regelsamlingen: OWASP 3.0
      - Lyssnare: Port 443
* **Azure Storage**
    - (7) Geo-redundant lagringskonton
* **Azure Backup**
    - (1) återställningstjänstvalvet
* **Azure Key Vault**
    - (1) key Vault
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
* **Azure Automation**
    - (1) automation-konto
* **Operations Management Suite**
    - (1) OMS-arbetsyta

## <a name="deployment-architecture"></a>Arkitektur för distribution

I följande avsnitt beskrivs element för utveckling och implementering.

### <a name="network-segmentation-and-security"></a>Nätverkssegmentering och säkerhet

#### <a name="application-gateway"></a>Application Gateway

Arkitekturen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg för webbaserade program (Brandvägg) och OWASP RuleSet-metod aktiverad. Ytterligare funktioner är:

- [Slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS version 1.0 och v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Brandvägg läge)
- [Förebyggande läge](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 RuleSet-metod

#### <a name="virtual-network"></a>Virtuellt nätverk

Arkitekturen definierar ett privat virtuellt nätverk med ett adressutrymme för 10.200.0.0/16.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Den här lösningen distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och hantering av undernät i ett virtuellt nätverk. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på individuella undernät för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner.

Se konfigurationen för [Nätverkssäkerhetsgrupper](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) distribueras med den här lösningen. Organisationer kan konfigurera nätverkssäkerhetsgrupper genom att redigera filen ovan med [denna dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) som en vägledning.

Varje undernät har en dedikerad nätverkssäkerhetsgrupp (NSG):
- 1 NSG för Programgateway (LBNSG)
- 1 NSG för Jumpbox (MGTNSG)
- 1 NSG för primära och sekundära domänkontrollanter (ADNSG)
- 1 NSG för SQL-servrar och filresursvittne (SQLNSG)
- 1 NSG för Webbnivå (WEBNSG)

#### <a name="subnets"></a>Undernät

Varje undernät är associerad med dess motsvarande NSG.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i vila med hjälp av flera åtgärder för kryptering.

#### <a name="azure-storage"></a>Azure Storage

För att uppfylla kraven för datakryptering för data i vila kan använda alla lagringskonton [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>SQL Database

SQL-databasen är konfigurerad för att använda [Transparent Data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), som utför realtid kryptering och dekryptering av data och loggfilen filer att skydda information i vila. TDE ger försäkran om att lagrade data inte har omfattas obehörig åtkomst. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption används för krypterad Windows IaaS virtuella diskar. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utnyttjar funktionen BitLocker för Windows-volymkryptering för Operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault hjälper dig att kontrollera och hantera disk krypteringsnycklarna.

### <a name="logging-and-auditing"></a>Granskning och loggning

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) innehåller utförlig loggning av system- och användaraktivitet som systemhälsa. 

- **Aktivitetsloggar:**[aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om de åtgärder som utfördes på resurser i din prenumeration.  
- **Diagnostikloggar:**[diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som orsakat av varje resurs.   Dessa loggar är Windows-händelsesystemloggar, Azure storage-loggar, Key Vault granskningsloggar och Programgateway loggar för åtkomst och brandväggen.
- **Loggen arkivering:** Azure aktivitetsloggar och diagnostikloggar som kan anslutas till Azure Log Analytics för bearbetning, lagring och dashboarding. Kvarhållning konfigureras av användaren upp till 730 dag till bevarande organisation-specifika krav.

### <a name="secrets-management"></a>Hemligheter management

Lösningen använder Azure Key Vault för att hantera nycklar och hemligheter.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och tjänster. 
- Lösningen är integrerad med Azure Key Vault för att hantera IaaS virtuell disk-krypteringsnycklar och hemligheter.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker hanteringsfunktioner identitet i Azure-miljön.
- [Azure Active Directory (AD Azure)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten.
- Autentisering till ett kunden distribuerade webbprogram kan utföras med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) aktiverar exakt fokuserad åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumerationen och åtkomst till resurser kan begränsas baserat på användarrollen.
- En distribuerad IaaS Active Directory-instans innehåller Identitetshantering på OS-nivå för distribuerade IaaS virtuella datorer.
   
### <a name="compute-resources"></a>Beräkna resurser

#### <a name="web-tier"></a>Webbnivå

Lösningen distribuerar web nivå virtuella datorer i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Tillgänglighetsuppsättningar se till att de virtuella datorerna är fördelade på flera kluster isolerade maskinvara för att förbättra tillgänglighet.

#### <a name="database-tier"></a>Databasnivå

Lösningen distribuerar databasen nivå virtuella datorer i en Tillgänglighetsuppsättning som en [AlwaysOn-tillgänglighetsgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Always On availability group funktionen ger för hög tillgänglighet och katastrofåterställning funktioner. 

#### <a name="active-directory"></a>Active Directory

Alla virtuella datorer som distribuerats av lösningen är ansluten till domänen och principer för Active Directory-grupper används för att framtvinga säkerhet och efterlevnad konfigurationer på driftsystemnivå. Active Directory virtuella datorer distribueras i en Tillgänglighetsuppsättning.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (skyddsmiljö host)

Management-jumpbox (skyddsmiljö host) innehåller en säker anslutning för administratörer att komma åt distribuerade resurser. NSG: N som är associerade med management undernätet där den virtuella datorn jumpbox finns tillåter anslutningar enbart på TCP-port 3389 för RDP. 

### <a name="malware-protection"></a>Skydd mot skadlig kod

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för virtuella datorer innehåller realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara, med konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera eller köra på den skyddade virtuella datorer.

### <a name="patch-management"></a>Uppdateringshantering

Windows-datorer som distribueras med den här utkast Automation konfigureras som standard för att ta emot automatiska uppdateringar från Windows Update-tjänsten. Den här lösningen distribuerar också OMS Azure Automation-lösningen som distributioner kan skapas för att distribuera korrigeringsfiler till Windows-servrar när det behövs.

### <a name="operations-management"></a>Verksamhetsstyrning

#### <a name="log-analytics"></a>Log Analytics

[Logga Analytics](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Operations Management Suite (OMS) och som möjliggör insamling och analys av data som genereras av resurser i Azure och lokala miljöer.

#### <a name="oms-solutions"></a>OMS-lösningar

Följande OMS-lösningar är redan installerat som en del av den här lösningen:
- [AD-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Utvärdering av program mot skadlig kod](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Säkerhet och granskning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL-bedömning](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Hantering av uppdateringar](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Azure aktivitetsloggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Spårning av ändringar](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Dokumentation för efterlevnad

### <a name="customer-responsibility-matrix"></a>Kunden ansvar matris

Den [kundens ansvarsområden matrisen](https://aka.ms/blueprinthighcrm) (Excel-arbetsbok) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen anger om implementering ansvarsfullt för kontrollen är Microsoft, kund, ansvar för varje kontroll (eller kontroll kapitel) eller delas mellan två. 

### <a name="control-implementation-matrix"></a>Kontrollen implementering matris

Den [kontroll implementering matrisen](https://aka.ms/blueprintwacim) (Excel-arbetsbok) visar en lista över alla säkerhetskontroller som krävs av FedRAMP hög baslinjen. Matrisen anger för varje kontroll (eller kontroll kapitel) som är tilldelad en kund ansvarsfullt i kundens ansvarsområden matrisen 1) om utkast Automation implementerar kontrollen och 2) en beskrivning av hur implementeringen överensstämmer med den styra requirement(s). Det här innehållet är också tillgänglig [här](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här lösningen Azure utkast består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänsten för att distribuera resurser i Azure. Detaljerade stegvisa anvisningar finns [här](https://aka.ms/fedrampblueprintrepo). ***Obs: Den här lösningen distribueras till Azure Government.***

#### <a name="quickstart"></a>Snabbstart
1. Klona eller hämta [detta](https://aka.ms/fedrampblueprintrepo) GitHub-lagringsplatsen till din lokala arbetsstation.

2. Kör PowerShell-skript före distributionen: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klicka på knappen nedan, logga in på Azure-portalen, ange de obligatoriska parametrarna för ARM-mallen och klicka på **inköp**.

    [![Distribuera till Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.  
- Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.  
- Kunderna får kopiera och använda det här dokumentet som intern referens.  
- Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.  
- Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
- Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
