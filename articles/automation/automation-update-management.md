---
title: Uppdateringshantering i Azure
description: Den här artikeln är avsedd att hjälpa dig att förstå hur du använder Azure Update hanteringslösning för att hantera uppdateringar för Windows- och Linux-datorer.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c6ec168332d8a655d78c3deffe89f51f7d75a840
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751888"
---
# <a name="update-management-solution-in-azure"></a>Uppdateringshantering i Azure

Du kan använda uppdateringen hanteringslösning i Azure Automation för att hantera uppdateringar av operativsystemet för din Windows- och Linux-datorer som distribueras i Azure, lokala miljöer eller andra molntjänstleverantörer av. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Du kan aktivera uppdateringshantering för virtuella datorer direkt från Azure Automation-konto. Information om hur du aktiverar uppdateringshantering för virtuella datorer från ditt Automation-konto finns [hantera uppdateringar för flera virtuella datorer](manage-update-multi.md). Du kan också aktivera uppdateringshantering för en enskild virtuell dator från fönstret virtuell dator i Azure-portalen. Det här scenariot är tillgängliga för [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) och [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuella datorer.

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras genom att uppdatera Management använder följande konfigurationer för att genomföra utvärdering och distribution:

* Microsoft Monitoring Agent (MMA) för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Följande diagram visar en översikt över beteendet och dataflöde med hur lösningen utvärderar och tillämpar uppdateringar för alla Windows Server och Linux-datorer i en arbetsyta:

![Uppdatera processflöde för hantering](media/automation-update-management/update-mgmt-updateworkflow.png)

När en dator som utför en sökning för att kontrollera uppdateringskompatibilitet, vidarebefordrar agenten information gruppvis till Azure logganalys. På en Windows-dator kan utförs kompatibilitetsgenomsökningen var 12: e timme som standard. 

Förutom schema för genomsökning initieras genomsökningen efter uppdateringsefterlevnad inom 15 minuter om MMA startas innan installationen av uppdateringen och efter installationen av uppdateringen. 

För en Linux-dator utförs kompatibilitetsgenomsökningen var 3: e timme som standard. Om agenten MMA startas initieras en kompatibilitetsgenomsökningen inom 15 minuter.

Lösningen hur uppdaterade datorn utifrån vad datakälla du rapporter är konfigurerad för att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS synkroniserades med Microsoft Update skilja resultaten från Microsoft Updates visar. Det här är samma för Linux-datorer som är konfigurerad för att rapportera till en lokal lagringsplatsen i stället för till en offentlig lagringsplatsen.

> [!NOTE]
> Om du vill rapportera korrekt till tjänsten, kräver uppdateringshantering vissa URL: er och portar som ska aktiveras. Mer information om dessa krav finns [nätverk planera för hybrider](automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfritt* ingår inte i omfånget för distribution för Windows-datorer. Endast obligatoriska uppdateringar ingår i omfånget för distributionen. 

Schemalagd distribution definierar vilka datorer får de tillämpliga uppdateringarna genom att explicit ange datorer eller genom att välja en [datorgrupp](../log-analytics/log-analytics-computer-groups.md) som baseras på loggen söker i en specifik uppsättning datorer. Du kan också ange ett schema för att godkänna och ange en tidsperiod under vilken du kan installera uppdateringar. 

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa runbooks och runbooks kräver inte någon konfiguration. När en update-distribution skapas, skapar ett schema som startar en runbook med master update vid den angivna tidpunkten för datorerna som ingår i distributionen av uppdateringen. Master runbook startar en underordnad runbook på varje agent som utför installationen av obligatoriska uppdateringar.

Det datum och tid som anges i distributionen av uppdateringen köra måldatorerna distributionen parallellt. Före installationen utförs en sökning för att kontrollera att uppdateringarna är fortfarande krävs. Om uppdateringar som inte godkänts i WSUS, för WSUS-klientdatorer misslyckas distributionen av uppdateringen.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Typer av klient som stöds

I följande tabell visas en lista över operativsystem som stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder bara uppdatera bedömningar.         |
|Windows Server 2008 R2 SP1 och senare     |.NET framework 4.5 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 eller senare krävs. ([Hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet.  ([Hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. 'Yum' kräver klassificeringsbaserad uppdatering för att returnera säkerhetsdata som CentOS inte har direkt.         |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS och 16.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

### <a name="unsupported-client-types"></a>Typer av klient som inte stöds

I följande tabell visas de operativsystem som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientens operativsystem (till exempel Windows 7 och Windows 10) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds ej.       |

### <a name="client-requirements"></a>Klientkrav

#### <a name="windows"></a>Windows

De måste ha tillgång till Microsoft Update eller Windows-agenter måste konfigureras för att kommunicera med en WSUS-server. Du kan använda hantering av uppdateringar med System Center Configuration Manager. Mer information om integrationsscenarier finns [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). Den [Windows-agenten](../log-analytics/log-analytics-agent-windows.md) krävs. Agenten installeras automatiskt om du är onboarding en virtuell Azure-dator.

#### <a name="linux"></a>Linux

För Linux måste datorn har åtkomst till en update-databas. Uppdatera databasen kan vara privat eller offentlig. En Operations Management Suite (OMS) Agent för Linux som är konfigurerad för att rapportera till flera logganalys arbetsytor stöds inte med den här lösningen.

Information om hur du installerar OMS-Agent för Linux och hämta den senaste versionen finns [Operations Management Suite-Agent för Linux](https://github.com/microsoft/oms-agent-for-linux). Information om hur du installerar OMS-Agent för Windows finns i [Operations Management Suite-agenten för Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Behörigheter

Särskild behörighet krävs för att skapa och hantera distributioner. Mer information om dessa behörigheter finns i avsnittet [rollbaserad åtkomst - uppdateringshantering](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser. Resurserna läggs till ditt Automation-konto. De är antingen direktanslutna agenter eller i en Operations Manager-ansluten management group.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du aktiverar den här lösningen, konfigureras automatiskt en Windows-dator som är direkt ansluten till logganalys-arbetsytan som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen.

Alla Windows-datorer som hanteras av lösningen ingår i den **Hybrid worker grupper** som en **System hybrid worker-gruppen** för Automation-kontot. Lösningarna använda namngivningskonventionen *Hostname FQDN_GUID*. Du kan inte rikta grupperna med runbooks i ditt konto. De misslyckas om försök. Dessa grupper är avsedda att underlätta endast hanteringslösning.

Du kan lägga till Windows-datorer till en Hybrid Runbook Worker-grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din hanteringsgrupp för System Center Operations Manager är ansluten till logganalys-arbetsytan, installeras följande hanteringspaket i Operations Manager. Dessa hanteringspaket installeras på datorer som är direkt anslutna Windows när du har lagt till lösningen. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

Mer information om hur lösningen hanteringspaketen är uppdaterade finns [ansluta Operations Manager till logganalys](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Bekräfta att Azure-datorer är publicerats så

För att bekräfta att direkt anslutna datorer kommunicerar med Log Analytics efter några minuter kan du köra en följande loggen genomsöks.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Du kan granska följande information för att verifiera agent logganalys på en Windows-dator:

1. På Kontrollpanelen, öppna **Microsoft Monitoring Agent**. På den **Azure logganalys** fliken agenten visas följande meddelande: **i Microsoft Monitoring Agent har anslutits till logganalys**.
2. Öppna Windows-händelseloggen. Gå till **program och tjänster för Logs\Operations** och Sök efter händelse-ID 3000 och händelse-ID 5002 från källan **tjänstanslutning**. Dessa händelser anger att datorn har registrerats med logganalys-arbetsytan och tar emot konfigurationen.

Om agenten inte kan kommunicera med logganalys och agenten är konfigurerad för att kommunicera med internet via en brandvägg eller proxyserver server, kontrollerar du att brandvägg eller proxyserver servern är korrekt konfigurerad. Information om hur du verifierar att servern brandvägg eller proxyserver är korrekt konfigurerad finns [nätverkskonfigurationen för Windows-agenten](../log-analytics/log-analytics-agent-windows.md) eller [nätverkskonfigurationen för Linux-agenten](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Om Linux-system har konfigurerats för att kommunicera med en proxy eller OMS-Gateway och är onboarding den här lösningen, uppdatera det *proxy.conf* behörigheter för att ge gruppen omiuser läsbehörighet för filen med hjälp av följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nya Linux-agenter ha statusen **uppdaterade** efter en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta att en Operations Manager-hanteringsgrupp kommunicerar med Log Analytics, se [Validera Operations Manager-integrering med logganalys](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om uppdateringar från Windows-agenter och sedan startar installationen av obligatoriska uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om uppdateringar från Linux-agenter och sedan startar installationen av obligatoriska uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br/>En direkt anslutning från Operations Manager-agenten till logganalys krävs inte. Data skickas från hanteringsgruppen till logganalys-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

En sökning utförs två gånger per dag för varje hanterad Windows-dator. Var 15: e minut Windows API kallas för att söka efter de senaste uppdateringstiden att fastställa om status har ändrats. Om statusen har ändrats initierat en genomsökning för kompatibilitet. 

En sökning utförs var 3: e timme för varje hanterad Linux-dator.

Det kan ta mellan 30 minuter och 6 timmar för instrumentpanelen för att visa uppdaterade data från hanterade datorer.

## <a name="viewing-update-assessments"></a>Visa uppdatering bedömningar

Välj i ditt Automation-konto **uppdateringshantering** att visa status för dina datorer.

Den här vyn visar information om dina datorer som saknar uppdateringar, distributioner och schemalagd uppdateringsdistributioner. I den **kompatibilitet kolumnen**, du kan se datorn uppskattades senast. I den **uppdatera AGENT beredskap** kolumn och, du kan se om hälsotillståndet för update-agenten. Om det finns ett problem, Välj länken för att gå till felsökning dokumentation som hjälper dig att du lär dig vilka steg för att åtgärda problemet.

Om du vill köra en sökning i loggen som returnerar information om maskin-, update- eller distributionen, markerar du objektet i listan. Den **loggen Sök** fönstret öppnas med en fråga för det markerade objektet:

![Uppdatera standardvyn för hantering](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Installera uppdateringar

När uppdateringar utvärderas för alla Linux och Windows datorer i din arbetsyta, kan du installera nödvändiga uppdateringar genom att skapa en *uppdateringsdistribution*. En distribution är en schemalagd installation av uppdateringar som krävs för en eller flera datorer. Du kan ange datum och tid för distributionen och en dator eller grupp av datorer som ska inkluderas i omfånget för en distribution. Läs mer om datorgrupper i [Computer groups in Log Analytics](../log-analytics/log-analytics-computer-groups.md) (Datorgrupper i Log Analytics).

 När du inkluderar datorgrupper i din distribution, utvärderas gruppmedlemskap bara en gång, då schemat skapades. Efterföljande ändringar i en grupp visas inte. Undvik detta genom att ta bort distributionen schemalagd uppdatering och skapa den igen.

> [!NOTE]
> Windows-datorer som distribueras från Azure Marketplace som standard är inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till den här lösningen eller lägga till Windows-datorer till arbetsytan. Om du inte aktivt hantera uppdateringar med hjälp av den här lösningen, gäller standardbeteendet (för att automatiskt tillämpa uppdateringar).

Konfigurera om Unattended-uppgraderingspaket om du vill inaktivera automatiska uppdateringar för att undvika uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Information om hur du konfigurerar paketet finns [automatiska uppdateringar-avsnittet i handboken Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Virtuella datorer som har skapats från Red Hat Enterprise Linux (RHEL) på begäran-avbildningar som är tillgängliga i Azure Marketplace är registrerade för åtkomst av [Red Hat Update infrastruktur (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som har distribuerats i Azure. Alla Linux-distribution måste uppdateras från databasen för den distribution online-filen genom att följa distributionsmetoder som stöds.

## <a name="view-missing-updates"></a>Visa uppdateringar som saknas

Välj **saknar uppdateringar** att visa listan över uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om hur många datorer som kräver uppdateringen, operativsystemet och en länk för mer information visas. Den **loggen Sök** visar mer information om uppdateringarna.

## <a name="view-update-deployments"></a>Visa distributioner

Välj den **uppdatera distributioner** att visa listan över befintliga distributioner. Markera en update-distributioner i tabellen för att öppna den **uppdatera distributionen kör** rutan för att distributionen av uppdateringen.

![Översikt över distributionsresultat för uppdatering](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Skapa eller redigera en uppdateringsdistribution

Om du vill skapa en ny uppdateringsdistribution **schema distribution**. Den **distribution av nya** fönstret öppnas. Ange värden för de egenskaper som beskrivs i följande tabell:

| Egenskap  | Beskrivning |
| --- | --- |
|Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Välj **Linux** eller **Windows**.|
|Datorer för att uppdatera |Välj en sparad sökning eller välj **datorn** från listrutan och välj sedan enskilda datorer. |
|Uppdatera klassificeringar|Välj de uppdateringsklassificeringar som du behöver. CentOS stöder inte det direkt.|
|Uppdateringar som ska uteslutas|Ange uppdateringar ska undantas. Ange i KB-artikel utan för Windows, den **KB** prefix. Ange paketnamnet för Linux eller Använd ett jokertecken.  |
|Schemainställningar|Välj tid för start och välj sedan antingen **när** eller **återkommande** för återkommande.|| Underhållsperiod |Antal minuter som anges för uppdateringar. Värdet får inte vara mindre än 30 minuter eller mer än 6 timmar. |

## <a name="update-classifications"></a>Uppdatera klassificeringar

I tabellerna nedan listas uppdateringsklassificeringar i uppdatera hantering med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett specifikt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning med snabbkorrigeringar som är packade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktlansering.        |
|Service pack     | En kumulativ uppsättning snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus- eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att utföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering för ett program eller en fil som är installerad.        |

### <a name="linux"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett specifikt problem eller en produktspecifik, säkerhetsrelaterad fråga.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är nödvändiga till sin natur eller inte är säkerhetsuppdateringar.        |

För Linux uppdateringshantering kan skilja mellan kritiska uppdateringar och säkerhetsuppdateringar i molnet medan du visar assessment data på grund av berikande data i molnet. För korrigering, uppdateringshantering förlitar sig på klassificering data är tillgängliga på datorn. Till skillnad från andra distributioner, CentOS har inte den här informationen som är tillgängliga direkt. Om du har CentOS datorer som har konfigurerats på ett sätt att returnera säkerhetsdata för kommandot kommer uppdateringshantering att kunna korrigering utifrån klassificeringarna.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metoden stöds-metod för att aktivera inbyggda klassificering-tillgänglighet på CentOS. För tillfället tillhandahålls endast bästa support för kunder som kanske har aktiverat detta på egen hand.

## <a name="ports"></a>Portar

Följande adresser krävs för hantering av uppdateringar. Kommunikation till dessa adresser sker via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

Mer information om portar som Hybrid Runbook Worker kräver finns [Hybrid Worker-rollen portar](automation-hybrid-runbook-worker.md#hybrid-worker-role).

## <a name="search-logs"></a>Sökloggar

Du kan göra sökningar mot loggarna utöver de information som tillhandahålls på Azure-portalen. På sidorna lösning väljer **logganalys**. Den **loggen Sök** fönstret öppnas.

Du kan också lära dig hur du anpassa frågor eller använda dem från olika klienter och mer genom att besöka: [logganalys seach API-dokumentationen](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Exempelfrågor

Följande avsnitt innehåller loggen Exempelfrågor för update-poster som samlas in av den här lösningen:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Den enda Azure VM Assessment frågor (Windows)

Ersätt värdet för VMUUID med VM-GUID för den virtuella datorn du frågar. Du kan hitta VMUUID som ska användas genom att köra följande fråga i logganalys: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Översikt över uppdateringar som saknas

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Enda Azure VM assessment frågor (Linux)

För vissa Linux-distributioner som det finns en [endian](https://en.wikipedia.org/wiki/Endianness) inte överensstämmer med värdet VMUUID som kommer från Azure Resource Manager och vad som lagras i logganalys. Följande fråga söker efter en matchning på antingen endian. Ersätt värdena VMUUID med big endian och little endian-formatet för GUID sökresultat korrekt. Du kan hitta VMUUID som ska användas genom att köra följande fråga i logganalys: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Översikt över uppdateringar som saknas

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Flera Virtuella assessment frågor

##### <a name="computers-summary"></a>Översikt över datorer

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Översikt över uppdateringar som saknas

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Listan med datorer

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Saknade uppdateringar lista

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="integrate-with-system-center-configuration-manager"></a>Integrera med System Center Configuration Manager

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av ett styrka och förfall av Configuration Manager för att hantera programuppdateringar. Configuration Manager är en del av deras management (SUM) uppdateringscykel programvara.

Information om hur du integrerar hanteringslösningen med System Center Configuration Manager finns [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Korrigering av Linux-datorer

I följande avsnitt beskrivs möjliga problem med Linux-korrigering.

### <a name="unexpected-os-level-upgrades"></a>Oväntat OS-nivå uppgraderingar

På vissa varianter i Linux, till exempel Red Hat Enterprise Linux kan OS-nivå uppgraderingar uppstå via paket. Detta kan leda till uppdateringshantering körs där versionsnumret OS ändras. Eftersom uppdatera Management använder samma metoder för att uppdatera paket som en administratör vill använda lokalt på Linux-datorn, är detta avsiktligt.

För att undvika att uppdatera versionen av Operativsystemet via uppdateringshantering körs kan använda den **undantag** funktion.

I Red Hat Enterprise Linux är paketnamnet för att utesluta server.x86_64-redhat-versionen.

![Paket för att utesluta för Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritiska / säkerhetskorrigeringar används inte

När du distribuerar uppdateringar till en Linux-dator kan du välja klassificeringar för uppdatering. Detta filtrerar de uppdateringar som tillämpas på de som uppfyller de angivna kriterierna. Det här filtret tillämpas lokalt på datorn när uppdateringen har distribuerats.

Eftersom uppdatera Management utför uppdateringen berikande i molnet, kan vissa uppdateringar flaggas i uppdatera hantering som har betydelse för säkerhet, även om den lokala datorn inte har denna information. Därför, om du använder kritiska uppdateringar till en Linux-dator, kan det finnas uppdateringar som inte markeras som hade säkerhet inverkan på datorn och uppdateringarna inte tillämpas.

Uppdatera Management kanske fortfarande rapporterar att datorn som icke-kompatibel eftersom den har mer information om den aktuella uppdateringen.

Distribuera uppdateringar med klassificeringen fungerar inte på CentOS direkt. För SUSE, välja *endast* andra uppdateringar klassificeringen resultera i att vissa uppdateringar att installeras också om säkerhetsuppdateringar rör zypper (package manager) eller dess beroenden krävs först. Detta är en begränsning i zypper. I vissa fall kan du behöva köra igen distributionen av uppdateringen att verifiera Kontrollera update-loggen.

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller information som hjälper dig att felsöka problem med lösningen för hantering av uppdateringar.

### <a name="windows"></a>Windows

Om du får problem när du försöker publicera lösningen eller en virtuell dator, kontrollera den **program och tjänster för Logs\Operations** händelseloggen på den lokala datorn för händelser med händelse-ID 4502 och händelse meddelanden innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. Följande tabell visar felmeddelanden och en möjlig lösning för varje:

| Meddelande | Orsak | Lösning |
|----------|----------|----------|
| Det gick inte att registrera datorn för uppdateringshantering.<br/>Registreringen misslyckades med undantaget<br/>System.InvalidOperationException: {"Meddelande":"Datorn har redan<br/>registrerats för ett annat konto. "} | Datorn är redan publicerats så att en annan arbetsyta för hantering av uppdateringar. | Utföra rensning av gamla artefakter av [bort Hybrid Runbook gruppen](automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group).|
| Det gick inte att registrera datorn för uppdateringshantering, registrering misslyckades med undantaget<br/>System.Net.Http.HttpRequestException: Ett fel uppstod när begäran skickades. ---><br/>System.Net.WebException: Den underliggande anslutningen<br/>stängdes: Ett oväntat fel<br/>uppstod vid mottagning. ---> System.ComponentModel.Win32Exception:<br/>Klienten och servern kan inte kommunicera<br/>eftersom de inte har en gemensam algoritm. | Proxy-gateway-brandväggen blockerar kommunikation. | [Granska nätverkskrav](automation-hybrid-runbook-worker.md#network-planning).|
| Det gick inte att registrera datorn för uppdateringshantering.<br/>Registreringen misslyckades med undantaget<br/>Newtonsoft.Json.JsonReaderException: Det gick inte att parsa positivt oändligt värde. | Proxy-gateway-brandväggen blockerar kommunikation. | [Granska nätverkskrav](automation-hybrid-runbook-worker.md#network-planning).|
| Certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com<br/>utfärdades inte av en certifikatutfärdare<br/>som används för Microsoft-tjänster. Kontakt<br/>nätverksadministratören för att se om de kör en proxy som hindrar<br/>TLS/SSL-kommunikation. |Proxy-gateway-brandväggen blockerar kommunikation. | [Granska nätverkskrav](automation-hybrid-runbook-worker.md#network-planning).|
| Det gick inte att registrera datorn för uppdateringshantering.<br/>Registreringen misslyckades med undantaget<br/>AgentService.HybridRegistration.<br/>PowerShell.Certificates.CertificateCreationException:<br/>Det gick inte att skapa ett självsignerat certifikat. ---><br/>System.UnauthorizedAccessException: Åtkomst nekad. | Självsignerade certifikat generation fel. | Kontrollera att systemkontot har<br/>läsbehörighet till mappen:<br/>**C:\ProgramData\Microsoft\**<br/>** Crypto\RSA **|

### <a name="linux"></a>Linux

Om uppdateringen körs inte starta på en Linux-dator, göra en kopia av följande loggfil och bevara för felsökning:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

Om fel uppstår under en uppdatering som körs när den startar på Linux, kontrollera utdata från den berörda datorn kör jobbet. Du kan hitta felmeddelanden från din dator Pakethanteraren som du kan undersöka och vidta åtgärder för. Uppdateringshantering kräver package manager ska felfri för lyckade uppdateringsdistributioner.

Paketet uppdateringar kan i vissa fall kan störa uppdatera Management hindrar en distribution från att slutföras. Om du ser som du måste antingen undanta dessa paket från framtida uppdateringskörningar eller installera dem manuellt själv.

Om du inte kan lösa ett problem med uppdatering, göra en kopia av följande loggfil och bevara den **innan** nästa uppdateringsdistributionen startar i felsökningssyfte:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Nästa steg

Fortsätta att guiden för att lära dig hur du hanterar uppdateringar för Windows-datorer.

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure](automation-tutorial-update-management.md)

* Använd loggen söker i [logganalys](../log-analytics/log-analytics-log-searches.md) att visa detaljerad uppdatera data.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) när viktiga uppdateringar har identifierats som saknas från datorer eller om en dator har automatiska uppdateringar har inaktiverats.
