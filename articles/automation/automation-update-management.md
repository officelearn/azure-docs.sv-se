---
title: Uppdateringshantering i Azure
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder den här lösningen för att hantera uppdateringar för dina Windows- och Linux-datorer.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/23/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83204132a7494dc766420520eeaf2362c98d878f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801821"
---
# <a name="update-management-solution-in-azure"></a>Uppdateringshantering i Azure

Lösning för hantering av uppdateringar i Azure automation kan du hantera uppdateringar av operativsystemet för Windows och Linux-datorer distribueras i Azure, lokala miljöer eller andra molntjänstleverantörer av. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Du kan aktivera uppdateringshantering för virtuella datorer direkt från Azure Automation-konto.
Information om hur du aktiverar uppdateringshantering för virtuella datorer via ditt Automation-konto finns i [Hantera uppdateringar för flera virtuella datorer](manage-update-multi.md). Du kan också aktivera uppdateringshantering för en enskild virtuell dator från sidan virtuell dator i Azure-portalen. Det här scenariot är tillgängliga för båda [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) och [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuella datorer.

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras av update management används följande konfigurationer för att utföra bedömning och uppdatera distributioner:

* Microsoft Monitoring agent för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services för Windows-datorer

Följande diagram visar en översikt över beteendet och dataflöde med hur lösningen utvärderar och tillämpar uppdateringar till alla anslutna Windows Server- och Linux-datorer i en arbetsyta.

![Uppdatera processflöde för hantering](media/automation-update-management/update-mgmt-updateworkflow.png)

När en dator som utför en sökning för att kontrollera uppdateringskompatibilitet, vidarebefordrar agenten information gruppvis till logganalys. På en Window-dator utförs en kompatibilitetssökning var 12:e timme som standard. Förutom schema för genomsökning initieras genomsökningen efter uppdateringsefterlevnad inom 15 minuter om Microsoft Monitoring Agent (MMA) startas innan uppdateringsinstallationen och efter installationen av uppdateringen. Kompatibilitetsgenomsökningen utförs var 3:e timme som standard med en Linux-dator och en kompatibilitetsgenomsökning initieras inom 15 minuter efter att MMA-agenten startas om.

Lösningen rapporterar hur uppdaterad datorn är utifrån vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS kan resultatet variera beroende på vad Microsoft Updates visar, beroende på när WSUS senast synkroniserades med Microsoft Update. Det här är samma för Linux-datorer som är konfigurerad för att rapportera till en lokal lagringsplatsen jämfört med en offentlig lagringsplatsen.

> [!NOTE]
> Uppdateringshantering kräver vissa URL: er och portar måste vara aktiverat för att korrekt rapportera tjänsten, se [nätverk planera för hybrider](automation-hybrid-runbook-worker.md#network-planning) lära dig mer om dessa krav.

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfria* ingår inte i distributionsomfattningen för Windows-datorer, utan endast nödvändiga uppdateringar. Schemalagd distribution definierar vilka datorer ta emot uppdateringar, antingen genom att explicit ange datorer eller välja en [datorgrupp](../log-analytics/log-analytics-computer-groups.md) som baseras på loggen söker i en viss uppsättning datorer. Du kan även ange ett schema för att godkänna och ange en tidsperiod när uppdateringar ska kunna installeras. Uppdateringar installeras av runbooks i Azure Automation. Du kan inte kan se dessa runbook-flöden och de kräver inte någon konfigurering. När en uppdateringsdistribution skapas så skapar den ett schema som startar en masteruppdaterings-runbook vid den angivna tidpunkten för de datorer som ingår. Denna master-runbook startar en underordnad runbook på varje agent som utför installationen av de nödvändiga uppdateringarna.

Vid det datum och den tid som anges i uppdateringsdistributionen kör måldatorerna distributionen parallellt. En sökning utförs först för att kontrollera om uppdateringarna fortfarande krävs och installerar dem. Om uppdateringarna inte godkänts i WSUS, för WSUS-klientdatorer misslyckas distributionen av uppdateringen.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Typer av klient som stöds

I följande tabell visas en lista över operativsystem som stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder endast uppdatera bedömningar         |
|Windows Server 2008 R2 SP1 och senare     |Windows PowerShell 4.0 eller senare krävs ([hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)).</br> Windows PowerShell 5.1 ([hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) rekommenderas för ökad tillförlitlighet.         |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

### <a name="unsupported-client-types"></a>Typer av klient som inte stöds

I följande tabell visas de operativsystem som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientens operativsystem (Windows 7, Windows 10, etc.) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds inte       |

### <a name="client-requirements"></a>Klientkrav

#### <a name="windows"></a>Windows

Windows-agenter måste konfigureras för att kommunicera med en Windows Server Update Services (WSUS)-server eller har tillgång till Microsoft Update. Uppdateringshantering kan användas med System Center Configuration Manager, att lära dig mer om integrationsscenarier finns [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). Den [Windows-agenten](../log-analytics/log-analytics-agent-windows.md) krävs. Den här agenten installeras automatiskt om du är en Azure-virtuella datorn.

#### <a name="linux"></a>Linux

För Linux måste datorn har åtkomst till en update-databas, som kan vara privat eller offentlig. En OMS-Agent för Linux konfigurerad för att rapportera till flera logganalys arbetsytor stöds inte med den här lösningen.

Mer information om hur du installerar OMS-Agent för Linux och hämta den senaste versionen finns [Operations Management Suite-Agent för Linux](https://github.com/microsoft/oms-agent-for-linux). Information om hur du installerar OMS-agenten för Windows finns i [Operations Management Suite-agenten för Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Behörigheter

Särskild behörighet krävs för att skapa och hantera distributioner. Läs mer om dessa behörigheter finns [roll baserad åtkomst - uppdateringshantering](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser som läggs till i ditt Automation-konto och ansluter direkt agenter eller Operations Manager-anslutna hanteringsgrupper.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du aktiverar den här lösningen, konfigureras automatiskt en Windows-dator som är direkt ansluten till logganalys-arbetsytan som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. För varje Windows-dator som hanteras av lösningen, det finns under sidan Hybrid worker-grupper som en System hybrid worker-gruppen för Automation-kontot följande namngivningskonvention *Hostname FQDN_GUID*. Du kan inte rikta grupperna med runbooks i ditt konto, annars de misslyckas. Grupperna är endast avsedda att stödja hanteringslösningen.

Du kan emellertid lägga till Windows-datorer till en Hybrid Runbook Worker-grupp i ditt Automation-konto så att den stöder runbook-flöden för Automation så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din hanteringsgrupp för System Center Operations Manager är ansluten till logganalys-arbetsytan, installeras följande hanteringspaket i Operations Manager. Dessa hanteringspaket också har installerats på direktanslutna Windows-datorer när du lägger till den här lösningen. Det finns inget att konfigurera eller hantera med dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Bekräfta Azure-datorer har publicerats så

Bekräfta direktanslutna datorer kommunicerar med Log Analytics efter några minuter som du kan köra följande loggen sökningen:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Du kan granska följande för att verifiera agent logganalys på en Windows-dator:

1. Öppna Microsoft Monitoring Agent på Kontrollpanelen och på den **Azure logganalys** fliken agenten visas ett meddelande om: **i Microsoft Monitoring Agent har anslutits till logganalys** .
2. Öppna Windows Event Log, gå till **Program- och tjänstloggar\Operations Manager** och sök efter händelse-ID 3000 och 5002 från källans tjänstanslutning. Dessa händelser anger datorn har registrerats med logganalys-arbetsytan och tar emot konfigurationen.

Om agenten är inte kan kommunicera med logganalys och den är konfigurerad för att kommunicera med internet genom en brandvägg eller proxyserver, bekräftar du att servern brandvägg eller proxyserver har konfigurerats korrekt genom att granska [nätverkskonfigurationen för Windows-agenten](../log-analytics/log-analytics-agent-windows.md) eller [nätverkskonfigurationen för Linux-agenten](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Om Linux-system har konfigurerats för att kommunicera med en proxy- eller OMS-Gateway och onboarding använder den här lösningen, uppdatera den *proxy.conf* behörigheter för att ge gruppen omiuser läsbehörighet för filen genom att utföra följande kommandon: `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nya Linux-agenter visar statusen **Uppdaterad** när en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta en hanteringsgrupp för Operations Manager kommunicerar med Log Analytics, se [Validera Operations Manager Integration with Log Analytics](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.</br>En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Data skickas från hanteringsgruppen till logganalys-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

För varje hanterad Windows-dator utförs en genomsökning två gånger per dag. Var 15:e minut anropas Windows API för att fråga efter den senaste uppdateringstiden för att fastställa om statusen har ändrats, och i så fall om en fullständig genomsökning har initierats. För varje hanterad Linux-dator utförs en sökning var tredje timme.

Det kan ta alltifrån 30 minuter upp till 6 timmar för instrumentpanelen att visa uppdaterade data från hanterade datorer.

## <a name="viewing-update-assessments"></a>Visning av kontroll av uppdateringar

Klicka på den **uppdateringshantering** på ditt automation-konto för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer som saknar uppdateringar, distributioner och schemalagd uppdateringsdistributioner.

Du kan köra en sökning i loggen som returnerar information om maskin-, update- eller distributionen genom att markera objektet i listan. Då öppnas den **loggen Sök** sida med en fråga för det markerade objektet.

## <a name="installing-updates"></a>Installera uppdateringar

När uppdateringar har utvärderats för alla Linux- och Windows-datorer i din arbetsyta kan du installera nödvändiga uppdateringar genom att skapa en *Uppdateringsdistribution*. En distribution är en schemalagd installation av uppdateringar som krävs för en eller flera datorer. Du kan ange datum och tid för distributionen förutom den dator eller grupp av datorer som ska inkluderas i distribueringens omfång. Läs mer om datorgrupper i [Computer groups in Log Analytics](../log-analytics/log-analytics-computer-groups.md) (Datorgrupper i Log Analytics). När du inkluderar datorgrupper i din distribution utvärderas gruppmedlemskap bara en gång vid tid då schemat skapades. Efterföljande ändringar i en grupp visas inte. Undvik detta genom att ta bort den schemalagda distributionen och återskapa den.

> [!NOTE]
> Virtuella Windows-datorer som distribueras från Azure Marketplace som standard är inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det beteendet ändras inte när du har lagt till lösningen eller virtuella Windows-datorer i arbetsytan. Om du inte aktivt hanterar uppdateringar med den här lösningen används standardbeteendet (automatiskt tillämpa uppdateringar) gäller.

Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Mer information om hur du konfigurerar detta finns i avsnittet [automatiska uppdateringar i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

För virtuella datorer som skapats från de Red Hat Enterprise Linux (RHEL)-avbildningar på begäran som finns i Azure Marketplace är de registrerade för att få åtkomst till [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som är distribuerat i Azure. Andra Linux-distributioner måste uppdateras från distributionens fildatabas på nätet genom de metoder som distributionerna stöder.

## <a name="viewing-missing-updates"></a>Visa uppdateringar som saknas

Klicka på **saknar uppdateringar** att visa listan över uppdateringar som saknas på dina datorer. Varje uppdatering ingår i listan och visar information om antalet datorer som kräver uppdateringen, operativsystemet och länken för mer information. Varje uppdatering kan väljas och **loggen Sök** sidan visas och visar mer information om uppdateringarna.

## <a name="viewing-update-deployments"></a>Visa uppdateringsdistributioner

Klicka på den **uppdateringsdistributioner** att visa listan över befintliga distributioner. Klicka på någon av uppdateringsdistributioner i tabellen öppnas den **uppdatera distributionen kör** sidan för att distributionen av uppdateringen.

![Översikt över uppdateringsdistributionens resultat](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Skapa en uppdateringsdistribution

Skapa en ny uppdatera distribution genom att klicka på den **schema uppdateringsdistribution** längst upp på skärmen för att öppna den **nya uppdatera distributionen** sidan. Du måste ange värden för egenskaperna i följande tabell:

| Egenskap  | Beskrivning |
| --- | --- |
| Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Linux- eller Windows|
| Datorer för att uppdatera |Välj en sparad sökning eller Välj dator från listrutan och Välj enskilda datorer |
|Uppdatera klassificeringar|Välj de uppdateringsklassificeringar som du behöver|
|Uppdateringar som ska uteslutas|Ange alla KBs för att utesluta utan prefixet 'KB'|
|Schemainställningar|Välj tid för start och välj antingen en gång eller återkommande för återkommande|
| Underhållsperiod |Antal minuter som anges för uppdateringar. Värdet kan inte vara mindre än 30 minuter och mer än 6 timmar |

## <a name="update-classifications"></a>Uppdatera klassificeringar

Följande tabeller innehåller en lista över uppdateringsklassificeringar i uppdateringshantering tillsammans med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett specifikt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning med snabbkorrigeringar som är packade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktlansering.        |
|Service pack     | En kumulativ uppsättning snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus- eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering för ett program eller en fil som är installerad för närvarande.        |

### <a name="linux"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett specifikt problem eller en produktspecifik, säkerhetsrelaterad fråga.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är nödvändiga i karaktär eller säkerhet för uppdateringar.        |

## <a name="ports"></a>Portar

Följande adresser krävs för hantering av uppdateringar. Kommunikation till dessa adresser görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

Mer information om portar som krävs för Hybrid Runbook Worker, [Hybrid Worker-rollen portar](automation-hybrid-runbook-worker.md#hybrid-worker-role)

## <a name="search-logs"></a>Sökloggar

Förutom de information som finns i portalen kan sökningar göras mot loggarna. Med den **ändringsspårning** öppen, klicka på **logganalys**, öppnas den **loggen Sök** sidan

### <a name="sample-queries"></a>Exempelfrågor

Följande tabell innehåller exempel loggen söker efter uppdateringen innehåller information som samlas in av den här lösningen:

| Fråga | Beskrivning |
| --- | --- |
|Uppdatering</br>&#124;där UpdateState == ”krävs” och valfria == false</br>&#124;projektet dator, avdelning, KBID, klassificering, PublishedDate |Alla datorer med saknade uppdateringar</br>Lägg till ett av följande för att begränsa OS:</br>OSType = ”Windows”</br>OSType == ”Linux- |
| Uppdatering</br>&#124;där UpdateState == ”krävs” och valfria == false</br>&#124;Om datorn == ”ContosoVM1.contoso.com”</br>&#124;projektet dator, avdelning, KBID, produkt, PublishedDate |Saknade uppdateringar fören specifik dator (ersätt värdet med namnet på din egen dator)|
| Händelse</br>&#124;där EventLevelName == ”error” och datorer i ((uppdatering &#124; var (klassificering == ”säkerhetsuppdateringar” eller klassificering == ”kritiska uppdateringar”)</br>&#124;där UpdateState == ”krävs” och valfria == false </br>&#124;distinkta dator)) |Felhändelser för datorer som saknar kritiska uppdateringar eller säkerhetsuppdateringar |
| Uppdatering</br>&#124;där UpdateState == ”krävs” och valfria == false</br>&#124;unikt namn |Separata, saknade uppdateringar bland samtliga datorer |
| UpdateRunProgress</br>&#124;där InstallationStatus == ”misslyckades” </br>&#124;Sammanfatta AggregatedValue = count() av datorn, avdelning, UpdateRunName |Datorer med uppdateringar som misslyckades under en uppdateringskörning</br>Lägg till ett av följande för att begränsa OS:</br>OSType = ”Windows”</br>OSType == ”Linux- |
| Uppdatering</br>&#124;där OSType == ”Linux-</br>&#124;där UpdateState! = ”behövs inte” och (klassificering == ”kritiska uppdateringar” eller klassificering == ”säkerhetsuppdateringar”)</br>&#124;Sammanfatta AggregatedValue = count() per dator |Lista över alla Linux datorer, som har en tillgänglig Paketuppdatering, som åtgärdar problem kritiskt eller säkerhet |
| UpdateRunProgress</br>&#124;där UpdateRunName == ”DeploymentName”</br>&#124;Sammanfatta AggregatedValue = count() per dator|Datorer som har uppdaterats i den här uppdateringskörningen (ersätt värdet med uppdateringsdistributionens namn |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrera med System Center Configuration Manager

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av programuppdateringar som en del av sin cykel för hantering av programuppdatering (SUM).

Information om hur du integrerar hanteringslösningen med System Center Configuration Manager finns [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Korrigering av Linux-datorer

I följande avsnitt beskrivs möjliga problem med Linux-korrigering.

### <a name="package-exclusion"></a>Paketet undantag

På vissa varianter i Linux, till exempel Red Hat Enterprise Linux kan OS-nivå uppgraderingar ske via paket. Detta kan leda till uppdateringshantering körs där versionsnumret OS ändras. Eftersom uppdatera Management använder samma metoder för att uppdatera paket som en administratör skulle lokalt på Linux-datorn, är detta avsiktligt.

För att undvika att uppdatera versionen av Operativsystemet via uppdateringshantering körs kan du använda den **undantag** funktion.

Paketnamnet för att utesluta vore i Red Hat Enterprise Linux: redhat-versionen-server.x86_64

![Paket för att utesluta för Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Säkerhetsuppdateringar som inte används

När du distribuerar uppdateringar till en Linux-dator kan välja du klassificeringar för uppdatering. Detta filtrerar de uppdateringar som tillämpas på de som uppfyller de angivna kriterierna. Det här filtret tillämpas lokalt på datorn när uppdateringen har distribuerats. Eftersom uppdatera Management utför uppdateringen berikande i molnet, kan vissa uppdateringar flaggas i uppdatera hantering som har med Säkerhetspåverkan även om den lokala datorn inte har denna information. Därför om du använder kritiska uppdateringar till en Linux-dator, kan det finnas uppdateringar som inte är markerad som har med säkerhet inverkan på den datorn och inte tillämpas. Uppdatera hantering kan fortfarande rapporterar att datorn som icke-kompatibel eftersom den har mer information om den aktuella uppdateringen.

Distribuera uppdateringar med klassificeringen kanske inte fungerar på openSUSE Linux på grund av olika uppdatering modellen används.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller information för att hjälpa dig att felsöka fel med lösningen Hantering av uppdateringar.

Kontrollera om det uppstår problem vid försök att publicera lösningen eller en virtuell dator kan den **program och tjänster för Logs\Operations** händelseloggen på den lokala datorn för händelser med händelse-ID 4502 och händelse meddelande som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. Följande tabell illustrerar specifika felmeddelanden och en möjlig lösning för vart och ett.

| Meddelande | Orsak | Lösning |
|----------|----------|----------|
| Det gick inte att registrera datorn för uppdateringshantering.</br>Registreringen misslyckades med undantaget</br>System.InvalidOperationException: {"Meddelande":"Datorn har redan</br>registrerats för ett annat konto. "} | Datorn har redan integrerats på en annan arbetsyta för uppdateringshantering. | Rensa gamla artefakter genom att [ta bort hybridrunbookgruppen](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|
| Det gick inte att registrera datorn för uppdateringshantering, registrering misslyckades med undantaget</br>System.Net.Http.HttpRequestException: Ett fel uppstod när begäran skickades. ---></br>System.Net.WebException: Den underliggande anslutningen</br>stängdes: Ett oväntat fel</br>uppstod vid mottagning. ---> System.ComponentModel.Win32Exception:</br>Klienten och servern kan inte kommunicera</br>eftersom de inte har en gemensam algoritm. | Proxy/Gateway/Firewall blockerar kommunikationen. | [Granska nätverkskraven](automation-hybrid-runbook-worker.md#network-planning)|
| Det gick inte att registrera datorn för uppdateringshantering.</br>Registreringen misslyckades med undantaget</br>Newtonsoft.Json.JsonReaderException: Det gick inte att parsa positivt oändligt värde. | Proxy/Gateway/Firewall blockerar kommunikationen. | [Granska nätverkskraven](automation-hybrid-runbook-worker.md#network-planning)|
| Certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com</br>utfärdades inte av en certifikatutfärdare</br>som används för Microsoft-tjänster. Kontakt</br>nätverksadministratören för att se om de kör en proxy som hindrar</br>TLS/SSL-kommunikation. |Proxy/Gateway/Firewall blockerar kommunikationen. | [Granska nätverkskraven](automation-hybrid-runbook-worker.md#network-planning)|
| Det gick inte att registrera datorn för uppdateringshantering.</br>Registreringen misslyckades med undantaget</br>AgentService.HybridRegistration.</br>PowerShell.Certificates.CertificateCreationException:</br>Det gick inte att skapa ett självsignerat certifikat. ---></br>System.UnauthorizedAccessException: Åtkomst nekad. | Fel vid genereringen av ett självsignerat certifikat. | Kontrollera att systemkontot har</br>läsbehörighet till mappen:</br>**C:\ProgramData\Microsoft\**</br>** Crypto\RSA **|

## <a name="next-steps"></a>Nästa steg

Fortsätta att guiden för att lära dig hur du hanterar uppdateringar för Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure](automation-tutorial-update-management.md)

* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) för att visa detaljerad uppdateringsinformation.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) som visas när viktiga uppdateringar saknas på datorer eller när automatiska uppdateringar är inaktiverade för en dator.
