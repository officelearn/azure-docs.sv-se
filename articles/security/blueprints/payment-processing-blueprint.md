---
title: Azure säkerhet och efterlevnad modell - PCI DSS-kompatibel bearbetningen av betalningen miljöer
description: Azure säkerhet och efterlevnad modell - PCI DSS-kompatibel bearbetningen av betalningen miljöer
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: jomolesk
ms.openlocfilehash: 1b77aee3bceef13128ada34fb325240dda98bc41
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-security-and-compliance-blueprint---pci-dss-compliant-payment-processing-environments"></a>Azure säkerhet och efterlevnad modell - PCI DSS-kompatibel bearbetningen av betalningen miljöer

## <a name="overview"></a>Översikt

Betalningen bearbetning för PCI DSS-kompatibel miljöer vägledning för distribution av en lämplig för att hantera känslig betalkortsuppgifter PCI DSS-kompatibel plattform som en-tjänst (PaaS) miljö. Den innehåller en gemensam Referensarkitektur och förenklar införandet av Microsoft Azure. Det här utkastet illustrerar en lösning för att uppfylla behoven för organisationer som vill ha en molnbaserad metod för att minska belastningen och kostnaden för distribution.

Det här utkastet är utformad för att uppfylla kraven i strikta Payment Card Industry Data Security Standards (PCI DSS 3.2) för insamling, lagring och hämtning av betalkortsuppgifter. Den visar kreditkortdata (inklusive kort tal, förfallodatum och verifiering data) att hantera en säker och kompatibla flera nivåer miljö distribueras som en slutpunkt till slutpunkt Azure-baserade PaaS-lösning. Mer information om vilka PCI DSS 3.2 krav och den här lösningen finns [PCI DSS krav - High-Level översikt](pci-dss-requirements-overview.md).

Det här utkastet är avsett att utgöra grunden för kunder att bättre förstå kraven och ska inte användas som – i en produktionsmiljö. Distribuera ett program till den här miljön utan ändringar är inte tillräckliga för att helt uppfylla kraven för en PCI DSS-kompatibel lösning för en anpassad lösning. Observera följande:
- Det här utkastet ger en baslinje för att hjälpa kunderna att använda Microsoft Azure på ett sätt som PCI DSS-kompatibel.
- För att uppnå PCI DSS godkännande krävs att en auktoriserad kvalificerade säkerhet utvärderare (QSA) certifiera en kund lösning för produktion.
- Kunder ansvarar för att utföra lämpliga säkerhet och efterlevnad granskning av en lösning som skapats med denna grundläggande arkitektur kraven kan variera baserat på egenskaperna för varje kund implementering och geografisk plats.  

För en snabb överblick över hur den här lösningen fungerar, titta på den här [video](https://aka.ms/pciblueprintvideo) förklarar och visar dess distribution.

## <a name="solution-components"></a>Lösningskomponenter

Grundläggande arkitektur består av följande komponenter:

- **Arkitekturdiagram för**. Diagrammet visar referensarkitektur som används för Contoso Webstore lösningen.
- **Distributionsmallar**. I den här distributionen [Azure Resource Manager-mallar](/azure/azure-resource-manager/resource-group-overview#template-deployment) används för att automatiskt distribuera komponenterna i arkitekturen i Microsoft Azure genom att ange konfigurationsparametrar under installationen.
- **Automatisk distribution skript**. Dessa skript att distribuera lösningen för slutpunkt till slutpunkt. Skripten består av:
    - En installation av modulen och [global administratör](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) installationsskriptet används för att installera och kontrollera att PowerShell-moduler som krävs och global administratörsroller är korrekt konfigurerade.
    - En installation av PowerShell-skript används för att distribuera lösningen för slutpunkt till slutpunkt, som tillhandahålls via en .zip-fil och en .bacpac-fil som innehåller en förskapad demo webbprogrammet med [SQL-databas exempel](https://github.com/Microsoft/azure-sql-security-sample). innehåll. Källkoden för den här lösningen är tillgänglig för granskning [modell databasen] [kod lagringsplatsen]. 

## <a name="architectural-diagram"></a>Arkitekturdiagram

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Användarscenario

Du vilken löser användningsfallet nedan.

> Det här scenariot visas hur en fiktiv vi besvarar flytta sina betalkort bearbetning till en Azure-baserade PaaS-lösning. Lösningen hanterar insamling av grundläggande information, inklusive Betalningsdata. Lösningen behandla inte betalningar med den här kreditkortsinformation; När data samlas in, ansvarar kunder för att initiera och slutföra transaktioner med en Betalningsprocessor som. Mer information finns i [”granska och riktlinjer för genomförande”](https://aka.ms/pciblueprintprocessingoverview).

### <a name="use-case"></a>Användningsfall
En liten vi besvarar kallas *Contoso Webstore* är redo att flytta sina betalningssystemet till molnet. Microsoft Azure som värd för processen för att köpa och för att tillåta en clerk att samla in kreditkortsbetalningar från sina kunder har valts.

Administratören ute efter en lösning som kan distribueras snabbt för att uppnå sina mål för en moln-föddes lösning. Han använder den här--konceptbevis (POC) för att diskutera med sin intressenter hur Azure kan användas för att samla in, lagra och hämta betalkortsuppgifter samtidigt efterleva strikta Payment Card Industry Data Security Standard (PCI DSS) krav.

> Du ansvarar för att utföra lämpliga säkerhet och efterlevnad granskning av en lösning som skapats med den arkitektur som används av den här POC kraven kan variera baserat på egenskaperna för din implementering och geografisk plats. PCI DSS kräver att du arbetar direkt med en auktoriserad kvalificerade säkerhet utvärderare att certifiera produktionsklara-lösning.

### <a name="elements-of-the-foundational-architecture"></a>Element i grundläggande arkitektur

Grundläggande arkitektur är utformad med fiktiva följande element:

Domän-plats `contosowebstore.com`

Användarroller som används för att illustrera användningsfallet och ger inblick i användargränssnittet.

#### <a name="role-site-and-subscription-admin"></a>Roll: Platsen och prenumeration admin

|Objekt      |Exempel|
|----------|------|
|Användarnamn: |`adminXX@contosowebstore.com`|
| Namn: |`Global Admin Azure PCI Samples`|
|Användartyp av:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- Administratörskontot kan inte läsa kreditkortsinformation avmaskerad. Alla åtgärder har loggats.
- Administratörskontot kan inte hantera eller logga in på SQL-databas.
- Administratörskontot kan hantera Active Directory och prenumeration.

#### <a name="role-sql-administrator"></a>Roll: SQL-administratör

|Objekt      |Exempel|
|----------|------|
|Användarnamn: |`sqlAdmin@contosowebstore.com`|
| Namn: |`SQLADAdministrator PCI Samples`|
| Förnamn: |`SQL AD Administrator`|
|Efternamn: |`PCI Samples`|
|Användartyp av:| `Administrator`|

- Sqladmin-konto kan inte visa ofiltrerade kreditkortsinformation. Alla åtgärder har loggats.
- Kontot sqladmin kan hantera SQL-databas.

#### <a name="role-clerk"></a>Roll: Clerk

|Objekt      |Exempel|
|----------|------|
|Användarnamn:| `receptionist_EdnaB@contosowebstore.com`|
| Namn: |`Edna Benson`|
| Förnamn:| `Edna`|
|Efternamn:| `Benson`|
| Användartyp av: |`Member`|

Edna Benson är hanteraren receptionist och verksamhet. Hon ansvarar för att säkerställa att kundinformation är korrekt och fakturering har slutförts. Edna är användaren inloggad för all interaktion med Contoso Webstore demo-webbplatsen. Edna har följande behörigheter: 

- Edna kan skapa och läsa kundinformation
- Edna kan ändra kundinformation.
- Edna kan skriva över eller ersätta kreditkortsnummer, förfallodatum och CVV information.

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - uppskattade priser

Den här grundläggande arkitektur och exempel webbprogrammet har en månatlig avgift struktur och en användning kostnad per timme som måste beaktas vid bedömning av lösningen. Dessa kostnader kan beräknas med hjälp av den [Azure kostnadskrävande Kalkylatorn](https://azure.microsoft.com/pricing/calculator/). Från och med September 2017 månatliga uppskattade kostnaden för den här lösningen är ~ $2500 Detta omfattar en $ 1 000/månad användning kostnad för ASE v2. Dessa kostnader varierar beroende på hur användning och kan ändras. Har skyldighet att kunden beräkna de uppskattade kostnaderna för månatliga vid tidpunkten för distributionen för en mer tillförlitlig uppskattning. 

Den här lösningen används följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

>- Application Gateway
>- Azure Active Directory
>- Apptjänst-miljö v2
>- Log Analytics
>- Azure Key Vault
>- Nätverkssäkerhetsgrupper
>- Azure SQL-databas
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Azure-webbapp
>- Azure Automation
>- Azure Automation-Runbooks
>- Azure DNS
>- Azure Virtual Network
>- Azure virtuell dator
>- Azure-resursgrupp och principer
>- Azure Blob Storage
>- Azure Active Directory-rollbaserad åtkomstkontroll (RBAC)

## <a name="deployment-architecture"></a>Arkitektur för distribution

I följande avsnitt beskrivs element för utveckling och implementering.

### <a name="network-segmentation-and-security"></a>Nätverkssegmentering och säkerhet

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

Grundläggande arkitektur minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg för webbaserade program (Brandvägg) och OWASP RuleSet-metod aktiverad. Ytterligare funktioner är:

- [Slutpunkt till slutpunkt SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivera [SSL-avlastning](/azure/application-gateway/application-gateway-ssl-portal)
- Inaktivera [TLS version 1.0 och v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brandvägg för webbaserade program](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (Brandvägg läge)
- [Förebyggande läge](/azure/application-gateway/application-gateway-web-application-firewall-portal) med OWASP 3.0 RuleSet-metod
- Aktivera [diagnostikloggning](/azure/application-gateway/application-gateway-diagnostics)
- [Anpassade hälsoavsökningar](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) och [Azure Advisor](/azure/advisor/advisor-security-recommendations) ger ytterligare skydd och meddelanden. Azure Security Center innehåller också ett rykte system.

#### <a name="virtual-network"></a>Virtuellt nätverk

Grundläggande arkitektur definierar ett privat virtuellt nätverk med ett adressutrymme för 10.0.0.0/16.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp (NSG):
- En DMZ nätverkssäkerhetsgrupp för brandväggen och programmet Gateway Brandvägg
- En NSG för hantering av jumpbox (skyddsmiljö host)
- En NSG för apptjänst-miljön

Var och en av de NSG: er har specifika portar och protokoll öppnas för säker och rätt drift av lösningen. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](#network-security-groups).

Var och en av de NSG: er har specifika portar och protokoll öppnas för en säker och korrekt fungerande lösningen. Dessutom kan är följande konfigurationer aktiverade för varje NSG:
- Aktiverad [diagnostikloggar och händelser](/azure/virtual-network/virtual-network-nsg-manage-log) lagras i storage-konto 
- Ansluten logganalys till den [NSGS diagnostik](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>Undernät
 Kontrollera varje undernät som är associerad med dess motsvarande NSG.

#### <a name="custom-domain-ssl-certificates"></a>Anpassad domän för SSL-certifikat
 HTTPS-trafik är aktiverad använder en anpassad domän för SSL-certifikat.

### <a name="data-at-rest"></a>Vilande data

Arkitekturen skyddar data i viloläge med hjälp av kryptering, granskning för databasen och andra åtgärder.

#### <a name="azure-storage"></a>Azure Storage

Till krypterade data i vila krav alla [Azure Storage](https://azure.microsoft.com/services/storage/) använder [Lagringstjänstens kryptering](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:

- [AD-autentisering och auktorisering](/azure/sql-database/sql-database-aad-authentication)
- [Granskning av SQL-databas](/azure/sql-database/sql-database-auditing-get-started)
- [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Regler i brandväggen](/azure/sql-database/sql-database-firewall-configure), så att för ASE arbetarpooler och hantering av IP-klient
- [SQL-Hotidentifiering](/azure/sql-database/sql-database-threat-detection-get-started)
- [Alltid krypterade kolumner](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL-databas dynamisk datamaskning](/azure/sql-database/sql-database-dynamic-data-masking-get-started), med hjälp av PowerShell-skript efter distributionen

### <a name="logging-and-auditing"></a>Granskning och loggning

[Logga Analytics](https://azure.microsoft.com/services/log-analytics) kan ge Contoso Webstore utförlig loggning för alla system- och användaraktivitet får innehålla Kortinnehavarens dataloggning. Ändringar kan granskas och verifiera noggrannhet. 

- **Aktivitetsloggar:**[aktivitetsloggar](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger kunskaper om de åtgärder som utfördes på resurser i din prenumeration.
- **Diagnostikloggar:**[diagnostikloggar](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som orsakat av varje resurs. Loggarna finns system händelseloggarna i Windows Azure Blob storage, tabeller och kön loggar.
- **Brandväggen loggarna:** Programgatewayen ger fullständig diagnostik och komma åt loggar. Brandväggsloggar är tillgängliga för Programgateway resurser som har en Brandvägg är aktiverad.
- **Loggen arkivering:** alla diagnostikloggar är konfigurerade för att skriva till en central och krypterad Azure storage-konto för arkivering med en definierad period (2 dagar). Loggarna är nu ansluten till Azure Log Analytics för bearbetning, lagring och dashboarding. [Logga Analytics](https://azure.microsoft.com/services/log-analytics) är en tjänst som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer.

### <a name="encryption-and-secrets-management"></a>Kryptering och hemligheter management

Contoso Webstore krypterar alla data som kreditkort och använder Azure Key Vault för hantering av nycklar, hindrar hämtning av CHD.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och tjänster. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) används för att kryptera alla Kortinnehavarens kundinformation förfallodatum och CVV.
- Data lagras på disk med [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) och BitLocker.

### <a name="identity-management"></a>Identitetshantering

Följande tekniker hanteringsfunktioner identitet i Azure-miljön.
- [Azure Active Directory (AD Azure)](https://azure.microsoft.com/services/active-directory/) är Microsofts flera innehavare molnbaserad katalog och identity management-tjänsten. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.
- Tillämpningen utförs autentiseringen med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Dessutom använder kolumnen databaskryptering också Azure AD autentisera programmet till Azure SQL Database. Mer information finns i [Always Encrypted: skydda känsliga data i SQL-databas](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory-identitetsskydd](/azure/active-directory/active-directory-identityprotection) identifierar potentiella problem som påverkar din organisations identiteter, konfigurerar du automatiska svar på identifierade misstänkta åtgärder som rör din organisations identiteter och undersöker misstänkta incidenter och vidtar lämpliga åtgärder som kan lösas.
- [Azure rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserad åtkomsthantering för Azure. Prenumerationen åtkomst begränsas till administratör för prenumeration och Azure Key Vault åtkomsten är begränsad till alla användare.

Mer information om hur du använder säkerhetsfunktionerna i Azure SQL-databasen finns på [Contoso kurs Demo-programmet](https://github.com/Microsoft/azure-sql-security-sample) exempel.
   
### <a name="web-and-compute-resources"></a>Webb- och beräkna resurser

#### <a name="app-service-environment"></a>App Service Environment

[Azure Apptjänst](/azure/app-service/) är en hanterad tjänst för att distribuera webbprogram. Contoso Webstore program distribueras som en [App Service Web App](/azure/app-service-web/app-service-web-overview).

[Azure Apptjänst-miljö (ASE v2)](/azure/app-service/app-service-environment/intro) är en funktion i App Service som tillhandahåller en helt isolerad och dedikerad miljö för säker körning av Apptjänst-appar i hög skala. Det är en Premium service-plan som används av den här grundläggande arkitektur för att aktivera PCI DSS.

ASEs är isolerad för att endast en enskild kund program som körs och alltid har distribuerats till ett virtuellt nätverk. Kunder har detaljerad kontroll över både inkommande och utgående nätverkstrafik och program kan upprätta säkra höghastighetsanslutning över virtuella nätverk till lokala företagsresurser.

Användning av ASEs för den här arkitekturen som tillåts för följande kontroller/konfigurationer:

- Värden i en skyddad virtuella nätverk och nätverk säkerhetsregler
- ASE som konfigurerats med ILB ett självsignerat certifikat för HTTPS-kommunikation
- [Intern belastningsutjämning läge](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (3-läge)
- Inaktivera [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) – TLS-protokollet som är inaktuell från en PCI DSS synvinkel
- Ändra [TLS-chiffer](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrollen [inkommande trafik N-/ skrivåtkomst portar](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [Brandvägg – begränsa Data](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Tillåt [trafik för SQL-databas](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (skyddsmiljö host)

Apptjänst-miljön är skyddad och låst, måste det finnas en mekanism för att tillåta för DevOps-versioner eller ändringar som kan krävas, till exempel möjligheten att övervaka webbprogrammet med Kudu. Virtuella datorn är skyddad bakom NAT belastningsutjämnare där du kan ansluta en virtuell dator på en annan port än TCP 3389. 

En virtuell dator har skapats som en jumpbox (skyddsmiljö host) med följande konfigurationer:

-   [Tillägg för program mot skadlig kod](/azure/security/azure-security-antimalware)
-   [OMS-tillägg](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-tillägget](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) med hjälp av Azure Key Vault (respekterar Azure Government, PCI DSS, HIPAA och andra krav).
-   En [automatisk avstängning princip](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) du minskar användningen av virtuella datorresurser som.

### <a name="security-and-malware-protection"></a>Säkerhet och skadlig kod protection

[Azure Security Center](https://azure.microsoft.com/services/security-center/) innehåller en centraliserad för säkerhetsläget för alla dina Azure-resurser. En överblick över kan du kontrollera att lämpliga säkerhetsåtgärder som är installerade och korrekt konfigurerad och att du snabbt kan identifiera alla resurser som kräver uppmärksamhet.  

[Azure Advisor](/azure/advisor/advisor-overview) är personliga molnet konsult som hjälper dig att följa bästa praxis för att optimera din Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) för Azure-molntjänster och virtuella datorer är realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara med konfigurerbara aviseringar när kända skadliga eller oönskade programvara försöker installeras eller köras på din Azure-system.

### <a name="operations-management"></a>Åtgärdshantering

#### <a name="application-insights"></a>Application Insights

Använd [Programinsikter](https://azure.microsoft.com/services/application-insights/) och få tillämplig insikter genom programhantering för prestanda och instant analyser.

#### <a name="log-analytics"></a>Log Analytics

[Logga Analytics](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer.

#### <a name="management-solutions"></a>Hanteringslösningar

Dessa ytterligare hanteringslösningar ska anses vara och konfigureras:
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure-nätverksanalys](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Spårning av ändringar](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Key Vault-analys](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Tjänstkarta](/azure/operations-management-suite/operations-management-suite-service-map)
- [Säkerhet och granskning](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Programvara mot skadlig kod](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Hantering av uppdateringar](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Security Center-integrering

Standarddistribution är avsedd att ge en basnivå för security center-rekommendationerna som anger konfigurationstillståndet felfri och säker. Du kan aktivera insamling av data från Azure Security Center. Mer information finns i [Azure Security Center - komma igång](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Distribuera lösningen

Komponenter för att distribuera den här lösningen finns i [PCI utkast kod databasen] [kod lagringsplatsen]. Distributionen av den grundläggande arkitekturen kräver flera steg utförs via Microsoft PowerShell v5. Du måste ange ett eget domännamn (t.ex contoso.com) för att ansluta till webbplatsen. Detta anges med hjälp av den `-customHostName` växla i steg 2. Mer information finns i [köpa ett anpassat domännamn för Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Ett anpassat domännamn krävs inte för att distribuera och köra lösningen, men du kan inte ansluta till webbplatsen i exempelsyfte.

Skripten lägga till domänanvändare i Azure AD-klient som du anger. Vi rekommenderar att du skapar en ny Azure AD-klienten ska användas som ett test.

Om det uppstår problem under distributionen finns [vanliga frågor och felsökning](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

Du rekommenderas att en ren installation av PowerShell används för att distribuera lösningen. Du kan också kontrollera att du använder de senaste moduler som krävs för korrekt körning av installationsskript. I det här exemplet vi logga in på jumpbox (skyddsmiljö host) och kör följande kommandon. Observera att detta gör att kommandot anpassade domäner.


1. Installera moduler som krävs och korrekt konfigurerade rollerna administratör.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Detaljerade instruktioner finns i [skript instruktioner - installationsprogrammet administratörskonto och behörigheten](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Installera lösningen-uppdateringshantering 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Detaljerade instruktioner finns i [skript instruktioner – distribuera och konfigurera Azure-resurser](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Loggning och övervakning. När lösningen har distribuerats, logganalys-arbetsytan kan öppnas och exempelmallarna i lösningen databasen kan användas för att illustrera hur en instrumentpanelen för övervakning kan konfigureras. Exempelmallarna finns i den [omsDashboards mappen](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Observera att måste samlas in i logganalys mallar för att distribuera på rätt sätt. Detta kan ta upp till en timme eller mer beroende på platsaktivitet.
 
    När du konfigurerar logganalys-loggning överväga att använda följande resurser:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Hotmodell

En data-Flödesdiagram (DFD) och exempel hotmodell för Contoso Webstore [modell Hotmodell](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Kunden ansvar matris

Kunder ansvarar för att behålla en kopia av den [ansvar sammanfattning matrisen](https://aka.ms/pciblueprintcrm32), vilket beskrivs PCI DSS kraven som är ansvar för kunden och de som har ansvar för Microsoft Azure.

## <a name="pci-compliance-review"></a>PCI-granskning 

Lösningen granskades av Coalfire systems, Inc. (PCI-DSS kvalificerade säkerhet bedömare). Den [vägledning för implementering och PCI efterlevnad granska](https://aka.ms/pciblueprintprocessingoverview) ger en granskare granskning av lösningen och överväganden för att överföra modell som en produktionsklara-distribution.

## <a name="disclaimer-and-acknowledgements"></a>FRISKRIVNING och bekräftelser

*September 2017*

- Det här dokumentet är endast i informativt syfte. MICROSOFT OCH AVYAN GÖR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.  
- Det här dokumentet innehåller inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft eller Avyan produkt eller lösningar.  
- Kunderna får kopiera och använda det här dokumentet som intern referens.  

  > [!NOTE]
  > Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.  

- Lösningen i det här dokumentet är avsett som en grundläggande arkitektur och får inte användas som-är för produktion. För att uppnå PCI-överensstämmelse krävs att kunder konsultera sina kvalificerade utvärderare säkerhet.  
- Alla kundnamn, transaktionsposter och eventuella relaterade data på den här sidan är fiktiva, skapas för denna grundläggande arkitektur och endast för jämförelseändamål. Ingen verklig associering eller koppling är avsedd och oavsiktliga ingen.  
- Den här lösningen har utvecklats gemensamt av Microsoft och Avyan rådgivning och är tillgängliga under den [MIT-licensen](https://opensource.org/licenses/MIT).
- Den här lösningen har granskats av Coalfire, Microsofts PCI-DSS granskare. Den [PCI efterlevnad granska](https://aka.ms/pciblueprintcrm32) ger en oberoende, från tredje part granskning av lösningen och komponenter som behöver åtgärdas. 
