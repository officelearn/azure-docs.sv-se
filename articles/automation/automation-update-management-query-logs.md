---
title: Frågeloggar för Azure Update Management
description: I den här artikeln beskrivs hur du frågar efter loggarna för uppdateringshantering på logganalysarbetsytan.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617416"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Frågeuppdateringsposter för uppdateringshantering i Azure Monitor-loggar

Förutom de uppgifter som finns i lösningen för uppdateringshantering kan du söka mot loggarna som lagras på logganalysarbetsytan. Välj Loggar i den vänstra rutan på **lösningssidan**. Sidan Loggsökning öppnas.

Du kan också lära dig hur du anpassar frågorna eller använder dem från olika klienter. Se [Dokumentation för logganalyssöknings-API](https://dev.loganalytics.io/).

## <a name="update-records"></a>Uppdateringsposter

Uppdateringshantering samlar in poster för virtuella datorer med Windows och Linux och de datatyper som visas i loggsökresultat. I följande avsnitt beskrivs dessa poster.

### <a name="required-updates"></a>Nödvändiga uppdateringar

En post med `RequiredUpdate` en typ av skapas som representerar uppdateringar som krävs av en dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domännamn för rapporteringsmaskinen. |
| KBID | Artikel-ID för Kunskapsbasen för Windows-uppdateringen. |
| ManagementGroupName | Namn på hanteringsgruppen för Operations Manager eller Log Analytics-arbetsytan. | 
| Produkt | De produkter som uppdateringen gäller för. | 
| PublishDate | Det datum då uppdateringen är klar att hämtas och installeras från Windows Update. |
| Server | | 
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows-agent-ID. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unik identifierare som representerar din organisationsinstans i Azure Active Directory. | 
| TimeGenerated | Datum och tid då posten skapades. | 
| Typ | *Uppdatera* | 
| Uppdateringsklassificering | Anger vilken typ av uppdateringar som kan tillämpas. För Windows:<br> *Kritiska uppdateringar*<br> *Säkerhetsuppdateringar*<br> *Samlade uppdateringar*<br> *Funktionspaket*<br> *Service pack*<br> *Definitionsuppdateringar*<br> *Verktyg*<br> *Uppdateringar*. För Linux:<br> *Kritiska uppdateringar och säkerhetsuppdateringar*<br> *Andra* |
| UppdaterarSeverity | Allvarlighetsgrad för säkerhetsproblemet. Värden är:<br> *Kritisk*<br> *Viktigt*<br> *Moderera*<br> *Låg* |
| UpdateTitle (Uppdateringsstå) | Titeln på uppdateringen.|

### <a name="update"></a>Uppdatera

En post med `Update` en typ av skapas som representerar tillgängliga uppdateringar och deras installationsstatus för en dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| GodkännandeKälla | Gäller endast windows-operativsystem. Källa för godkännande för posten. Värdet är Microsoft Update. |
| Godkända | Sant om posten godkänns eller Falskt på annat sätt. |
| Klassificering | Klassificering av godkännande. Värdet är Uppdateringar. |
| Dator | Fullständigt kvalificerat domännamn för rapporteringsmaskinen. |
| DatorMiljö | Miljö. Möjliga värden är Azure eller Non-Azure. |
| MSRCBulletinID | Id-nummer för säkerhetsbulletiner. | 
| MSRCSeverity | Allvarlighetsgrad för säkerhetsproblemet. Värden är:<br> Kritisk<br> Viktigt<br> Moderera<br> Låg |  
| KBID | Artikel-ID för Kunskapsbasen för Windows-uppdateringen. |
| ManagementGroupName | Namn på hanteringsgruppen för Operations Manager eller log analytics-arbetsytan. |
| UpdateID | Unik identifierare för programuppdateringen. |
| RevisionNumber | Revisionsnumret för en specifik revidering av en uppdatering. |
| Valfri | Sant om posten är valfri eller Falskt på annat sätt. | 
| RebootBehavior | Omstartsbeteendet efter installation/avinstallation av en uppdatering. |
| _ResourceId | Unik identifierare för den resurs som är associerad med posten. |
| Typ | Posttyp. Värdet är Uppdatera. |
| VMUUID (PÅ ANDRA) | Unik identifierare för den virtuella datorn. |
| MG | Unik identifierare för hanteringsgruppen eller Log Analytics-arbetsytan. | 
| TenantId | Unik identifierare som representerar organisationens instans av Azure Active Directory. | 
| SourceSystem | Källsystemet för posten. Värdet är `OperationsManager`. | 
| TimeGenerated | Datum och tid för att skapa post. | 
| SourceComputerId | Unik identifierare som representerar källdatorn. | 
| Titel | Titeln på uppdateringen. |
| PubliceradDate (UTC) | Det datum då uppdateringen är klar att hämtas och installeras från Windows Update.  |
| UpdateState | Det aktuella läget för uppdateringen. | 
| Produkt | De produkter för vilka uppdateringen är tillämplig. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. | 
| ResourceGroup | Namn på den resursgrupp som resursen tillhör. | 
| ResourceProvider | Resursleverantören. | 
| Resurs | Namn på resursen. | 
| ResourceType | Resurstypen. | 

### <a name="update-agent"></a>Uppdatera agent

En post med `UpdateAgent` en typ av skapas som innehåller information om uppdateringsagenten på datorn. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Ålder av ÄlskasteMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Dator | Fullständigt kvalificerat domännamn för rapporteringsmaskinen. |
| DagSinceLastUpdateBucket | | 
| ManagementGroupName | Namn på hanteringsgruppen för Operations Manager eller Log Analytics-arbetsytan. |
| OSVersion | Versionen av operativsystemet. |
| Server | |
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows-agent-ID. |
| SourceSystem | Källsystemet för posten. Värdet är `OperationsManager`. | 
| TenantId | Unik identifierare som representerar organisationens instans av Azure Active Directory. |
| TimeGenerated | Datum och tid för att skapa post. |
| Typ | Posttyp. Värdet är Uppdatera. | 
| WindowsUpdateAgentVersion | Version av Windows Update-agenten. |
| WSUSServer | Fel om Windows Update-agenten har problem för att hjälpa till med felsökning. |

### <a name="update-deployment-status"></a>Uppdatera distributionsstatus 

En post med `UpdateRunProgress` en typ av skapas som ger uppdateringsdistributionsstatus för en schemalagd distribution av dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domännamn för rapporteringsmaskinen. |
| DatorMiljö | Miljö. Värden är Azure eller Non-Azure. | 
| CorrelationId | Unik identifierare för runbook-jobbet körs för uppdateringen. |
| EndTime | Den tidpunkt då synkroniseringsprocessen avslutades. | 
| FelResultat | Felkod för Windows Update som genereras om en uppdatering inte kan installeras. | 
| InstallationStatus | Möjliga installationstillstånd för en uppdatering på klientdatorn,<br> `NotStarted`- Jobbet har inte utlösts än.<br> `FailedToStart`- det går inte att starta jobbet på maskinen.<br> `Failed`- jobbet startade men misslyckades med ett undantag.<br> `InProgress`- Pågående arbete.<br> `MaintenanceWindowExceeded`- om körningen kvar men underhållsfönstrets intervall uppnåddes.<br> `Succeeded`- jobbet lyckades.<br> `InstallFailed`- Uppdateringen kunde inte installeras.<br> `NotIncluded`<br> `Excluded` |
| KBID | Artikel-ID för Kunskapsbasen för Windows-uppdateringen. | 
| ManagementGroupName | Namn på hanteringsgruppen för Operations Manager eller Log Analytics-arbetsytan. |
| OSType | Typ av operativsystem. Värden är Windows eller Linux. | 
| Produkt | De produkter för vilka uppdateringen är tillämplig. |
| Resurs | Namn på resursen. | 
| ResourceId | Unik identifierare för den resurs som är associerad med posten. |
| ResourceProvider | Resursleverantören. | 
| ResourceType | Resurstyp. | 
| SourceComputerId | Unik identifierare som representerar källdatorn. | 
| SourceSystem | Källsystem för posten. Värdet är `OperationsManager`. |
| StartTime | Tid då uppdateringen är schemalagd att installeras. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. | 
| LyckadesOnRetry | Värde som anger om uppdateringskörningen misslyckades vid det första försöket och den aktuella åtgärden är ett försök att försöka igen. |
| TimeGenerated | Datum och tid för att skapa post. |
| Titel | Titeln på uppdateringen. |
| Typ | Typ av uppdatering. Värdet är `UpdateRunProgress`. |
| UpdateId (på)UpdateId) | Unik identifierare för programuppdateringen. |
| VMUUID (PÅ ANDRA) | Unik identifierare för den virtuella datorn. |
| ResourceId | Unik identifierare för den resurs som är associerad med posten. |

### <a name="update-summary"></a>Uppdatera sammanfattning 

En post med `UpdateSummary` en typ av skapas som ger uppdateringssammanfattning efter dator. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domännamn för rapporteringsmaskinen. |
| DatorMiljö | Miljö. Värden är Azure eller Non-Azure. | 
| CriticalUpdatesMissing | Antal viktiga uppdateringar som saknas. | 
| ManagementGroupName | Namn på hanteringsgruppen för Operations Manager eller Log Analytics-arbetsytan. |
| NETRuntimeVersion | Version av .NET Framework installerat på Windows-datorn. |
| OldestMissingSecurityUpdateBucket | Specificeraren av den äldsta saknade säkerhets bucketen. Värden är:<br> Senaste om värdet är mindre än 30 dagar<br> 30 dagar sedan<br> 60 dagar sedan<br> 90 dagar sedan<br> 120 dagar sedan<br> 150 dagar sedan<br> 180 dagar sedan<br> Äldre när värdet är större än 180 dagar. | 
| OldestMissingSecurityUpdateInDays | Totalt antal dagar för den äldsta uppdateringen som identifierats som tillämpligt och som inte har installerats. |
| OsVersion | Versionen av operativsystemet. |
| OtherUpdatesMissing | Antal upptäckta uppdateringar saknas. |
| Resurs | Namnet på resursen för posten. | 
| ResourceGroup | Namn på resursgruppen som innehåller resursen. |
| ResourceId | Unik identifierare för den resurs som är associerad med posten. |
| ResourceProvider | Resursleverantören. |
| ResourceType | Resurstyp. |
| RestartPending | Sant om en omstart väntar eller Falskt på annat sätt. |
| SecurityUpdatesMissing | Antal saknade säkerhetsuppdateringar som är tillämpliga.| 
| SourceComputerId | Unik identifierare för den virtuella datorn. |
| SourceSystem | Källsystem för posten. Värdet är `OpsManager`. | 
| SubscriptionId | Unik identifierare för Azure-prenumerationen. |
| TimeGenerated | Datum och tid för att skapa post. |
| TotalUpdatesMissing | Totalt antal saknade uppdateringar som är tillämpliga. | 
| Typ | Posttyp. Värdet är `UpdateSummary`. |
| VMUUID (PÅ ANDRA) | Unik identifierare för den virtuella datorn. |
| WindowsUpdateAgentVersion | Version av Windows Update-agenten. |
| WindowsUpdateSetting | Status för Windows Update-agenten. Möjliga värden:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Fel om Windows Update-agenten har problem för att hjälpa till med felsökning. |
| _ResourceId | Unik identifierare för den resurs som är associerad med posten. |

## <a name="sample-queries"></a>Exempelfrågor

I följande avsnitt finns exempelloggfrågor för uppdateringsposter som samlas in för uppdateringshantering.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Bekräfta att datorer som inte är Azure-datorer är inbyggda

Om du vill bekräfta att direkt anslutna datorer kommunicerar med Azure Monitor-loggar kör du en av följande loggsökningar.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

På en Windows-dator kan du granska följande information för att verifiera agentanslutning med Azure Monitor-loggar:

1. Öppna **Microsoft Monitoring Agent på**Kontrollpanelen . På fliken **Azure Log Analytics** visar agenten följande meddelande: Microsoft Monitoring Agent har anslutit till Log **Analytics**.
2. Öppna Windows-händelseloggen. Gå till **program- och tjänstloggar\Operations Manager** och sök efter händelse-ID 3000 och händelse-ID 5002 från **källtjänstkontakten**. Dessa händelser anger att datorn har registrerats på Log Analytics-arbetsytan och tar emot konfigurationen.

Om agenten inte kan kommunicera med Azure Monitor-loggar och agenten är konfigurerad för att kommunicera med internet via en brandvägg eller proxyserver, bekräftar du att brandväggen eller proxyservern är korrekt konfigurerad. Mer information om hur du verifierar att brandväggen eller proxyservern är korrekt konfigurerad finns i [Nätverkskonfiguration för Windows-agent](../azure-monitor/platform/agent-windows.md) eller [Nätverkskonfiguration för Linux-agent](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Om dina Linux-system är konfigurerade för att kommunicera med en proxy eller Log Analytics Gateway och du lägger till den här lösningen uppdaterar du *proxy.conf-behörigheterna* för att bevilja omiuser-gruppen läsbehörighet för filen med hjälp av följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nyligen tillagda Linux-agenter visar **statusen Uppdaterad** efter att en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

Information om hur du bekräftar att en Hanteringsgrupp för Operations Manager kommunicerar med Azure Monitor-loggar finns i [Verifiera Operations Manager-integrering med Azure Monitor-loggar](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Enskilda Azure VM-utvärderingsfrågor (Windows)

Ersätt VMUUID-värdet med VM-GUID för den virtuella datorn som du frågar efter. Du hittar VMUUID som ska användas genom att köra följande fråga i Azure Monitor-loggar:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar saknas

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista över saknade uppdateringar

```loganalytics
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

### <a name="single-azure-vm-assessment-queries-linux"></a>Enskilda azure VM-utvärderingsfrågor (Linux)

För vissa Linux-distributioner finns det en [endianness-obalans](https://en.wikipedia.org/wiki/Endianness) med VMUUID-värdet som kommer från Azure Resource Manager och vad som lagras i Azure Monitor-loggar. Följande fråga söker efter en matchning på båda endianness. Ersätt VMUUID-värdena med guidens stora endian- och little-endian-format för att korrekt returnera resultaten. Du hittar VMUUID som ska användas genom att köra följande fråga i Azure Monitor-loggar:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar saknas

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista över saknade uppdateringar

```loganalytics
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

### <a name="multi-vm-assessment-queries"></a>Utvärderingsfrågor för flera virtuella datorer

#### <a name="computers-summary"></a>Sammanfattning av datorer

```loganalytics
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
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Sammanfattning av uppdateringar saknas

```loganalytics
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
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista över datorer

```loganalytics
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

#### <a name="missing-updates-list"></a>Lista över saknade uppdateringar

```loganalytics
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

## <a name="next-steps"></a>Nästa steg

* Använd loggsökningar i [Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) för att visa detaljerade uppdateringsdata.
* [Skapa aviseringar](automation-tutorial-update-management.md#configure-alerts) för uppdateringsdistributionsstatus.
