---
title: Fråga Azure Uppdateringshantering-loggar
description: I den här artikeln beskrivs hur du frågar loggarna efter Uppdateringshantering i Log Analytics-arbetsytan.
services: automation
ms.subservice: update-management
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: f31168d47f31d8e740c95cb3d9e449f473cc78dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216850"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Fråga efter uppdaterings poster för Uppdateringshantering i Azure Monitor loggar

Utöver den information som finns i Uppdateringshantering lösning kan du söka mot loggarna som lagras i Log Analytics arbets ytan. På lösnings sidan väljer du **loggar**i det vänstra fönstret. Sidan för **loggs ökning** öppnas.

Du kan också lära dig hur du anpassar frågorna eller använder dem från olika klienter och mer genom att besöka: [Log Analytics Search API-dokumentation](https://dev.loganalytics.io/).

## <a name="update-records"></a>Uppdateringsposter

Poster som samlas in av Uppdateringshantering för virtuella Windows-och Linux-datorer och data typer som visas i loggs öknings resultat. I följande avsnitt beskrivs dessa poster.

### <a name="required-updates"></a>Nödvändiga uppdateringar

En post med en typ av `RequiredUpdate` skapas som representerar uppdateringar som krävs av en dator. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| KBID | Kunskaps bas artikel-ID för Windows Update. |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. | 
| Produkt | De produkter som uppdateringen gäller för. | 
| PublishDate | Det datum då uppdateringen är redo att laddas ned och installeras från Windows Update. |
| Server | | 
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows agent-ID. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unik identifierare som representerar organisations instansen av Azure Active Directory. | 
| TimeGenerated | Datum och tid då posten skapades. | 
| Typ | *Uppdatering* | 
| UpdateClassification | Anger vilken typ av uppdateringar som kan tillämpas. För Windows:<br> *Kritiska uppdateringar*<br> *Säkerhets uppdateringar*<br> *Samlade uppdateringar*<br> *Funktions paket*<br> *Service Pack*<br> *Definitions uppdateringar*<br> *Verktyg*<br> *Uppdateringar*. För Linux:<br> *Kritiska uppdateringar och säkerhets uppdateringar*<br> *Övrigt* |
| UpdateSeverity | Allvarlighets grad för säkerhets problemet. Värden är:<br> *Mindre*<br> *Viktigt*<br> *Måttlig*<br> *Börjar* |
| UpdateTitle | Uppdateringens rubrik.|

### <a name="update"></a>Uppdatera

En post med en typ av `Update` skapas som representerar tillgängliga uppdateringar och deras installations status för en dator. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| ApprovalSource | Gäller endast för Windows-operativsystem. Värdet är *Microsoft Update*. |
| Godkända | *Sant* eller *falskt* |
| Klassificering | *Uppdateringar* |
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | *Azure* eller *icke-Azure*. |
| MSRCBulletinID | Säkerhetsbulletin ID-nummer | 
| MSRCSeverity | Allvarlighets grad för säkerhets problemet. Värden är:<br> *Mindre*<br> *Viktigt*<br> *Måttlig*<br> *Börjar* |  
| KBID | Kunskaps bas artikel-ID för Windows Update. |
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| UpdateID | Unikt ID för program uppdateringen. |
| RevisionNumber | Revisions numret för en bestämd revision av en uppdatering. |
| Valfri | *Sant* eller *falskt* | 
| RebootBehavior | Omstarts beteendet efter installation/avinstallation av en uppdatering. |
| _ResourceId | Unik identifierare för den resurs som posten är associerad med. |
| Typ | *Uppdatering* |
| VMUUID | Unik identifierare för den virtuella datorn. |
| MG | Unik identifierare för hanterings gruppen eller Log Analytics arbets ytan. | 
| TenantId | Unik identifierare som representerar organisations instansen av Azure Active Directory. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Datum och tid då posten skapades. | 
| SourceComputerId | Unik identifierare som representerar käll datorn. | 
| Rubrik | Uppdateringens rubrik. |
| PublishedDate (UTC) | Det datum då uppdateringen är redo att laddas ned och installeras från Windows Update.  |
| UpdateState | Uppdateringens aktuella tillstånd. | 
| Produkt | De produkter som uppdateringen gäller för. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. | 
| ResourceGroup | Namnet på den resurs grupp som resursen är medlem i. | 
| ResourceProvider | Anger resurs leverantören. | 
| Resurs | Namn på resursen. | 
| ResourceType | Namnet på resurs typen. | 

### <a name="update-agent"></a>Uppdatera agent

En post med en typ av `UpdateAgent` skapas som innehåller information om uppdaterings agenten på datorn. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| OSVersion | Operativ systemets version. |
| Server | |
| SourceHealthServiceId | Unik identifierare som representerar Log Analytics Windows agent-ID. |
| SourceSystem | *OperationsManager* | 
| TenantId | Unik identifierare som representerar organisations instansen av Azure Active Directory. |
| TimeGenerated | Datum och tid då posten skapades. |
| Typ | *Uppdatering* | 
| WindowsUpdateAgentVersion | Windows Update agentens version. |
| WSUSServer | Visar fel om Windows Update Agent har problem att hjälpa med fel sökningen. |

### <a name="update-deployment-status"></a>Status för uppdaterings distribution 

En post med en typ av `UpdateRunProgress` skapas som tillhandahåller uppdaterings distributions status för en schemalagd distribution per dator. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | *Azure* eller *icke-Azure*. | 
| CorrelationId | Unik identifierare för Runbook-jobbet som körs för uppdateringen. |
| EndTime | Tiden då synkroniseringsprocessen avslutades. | 
| ErrorResult | Windows Update felkod som genereras om en uppdatering inte kan installeras. | 
| Updaterunprogress | Eventuella installations tillstånd för en uppdatering på klient datorn *pågår*, *lyckades*, *delvis misslyckades*. |
| KBID | Kunskaps bas artikel-ID för Windows Update. | 
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| OSType | Anger typ av operativ system, *Windows* eller *Linux*. | 
| Produkt | De produkter som uppdateringen gäller för. |
| Resurs | Namn på resursen. | 
| ResourceId | Unik identifierare för den resurs som posten är associerad med. |
| ResourceProvider | Anger resurs leverantören. | 
| ResourceType | Namnet på resurs typen. | 
| SourceComputerId | Unik identifierare som representerar käll datorn. | 
| SourceSystem | *OperationsManager* |
| StartTime | Tid när uppdateringen är schemalagd att installeras. |
| SubscriptionId | Unik identifierare för Azure-prenumerationen. | 
| SucceededOnRetry | Visar när uppdaterings körningen misslyckades för det första försöket och den aktuella åtgärden är ett försök att försöka igen. |
| TimeGenerated | Datum och tid då posten skapades. |
| Rubrik | Uppdateringens rubrik. |
| Typ | *UpdateRunProgress* |
| UpdateId | Unikt ID för program uppdateringen. |
| VMUUID | Unik identifierare för den virtuella datorn. |
| _ResourceId | Unik identifierare för den resurs som posten är associerad med. |

### <a name="update-summary"></a>Uppdaterings Sammanfattning 

En post med en typ av `UpdateSummary` skapas som tillhandahåller uppdaterings Sammanfattning per dator. Dessa poster har egenskaper i följande tabell:

| Egenskap | Beskrivning | 
|----------|-------------|
| Dator | Fullständigt kvalificerat domän namn för rapporterings datorn. |
| ComputerEnvironment | *Azure* eller *icke-Azure*. | 
| CriticalUpdatesMissing | Antalet kritiska uppdateringar som är tillämpliga. | 
| ManagementGroupName | Namnet på den Operations Manager hanterings gruppen eller Log Analytics arbets ytan. |
| NETRuntimeVersion | Versionen av .NET Framework installerad på Windows-datorn. |
| OldestMissingSecurityUpdateBucket | Värden är:<br> *Senaste*<br> *för 30 dagar sedan*<br> *för 60 dagar sedan*<br> *Tidigare* | 
| OldestMissingSecurityUpdateInDays | Totalt antal dagar för den äldsta uppdateringen som har identifierats som tillämpligt och som inte har installerats. |
| OsVersion | Operativ systemets version. |
| OtherUpdatesMissing | Antal identifierade uppdateringar som saknas. |
| Resurs |  Namn på resursen. | 
| ResourceGroup | Namnet på den resurs grupp som resursen är medlem i. |
| ResourceId | Unik identifierare för den resurs som posten är associerad med. |
| ResourceProvider | Anger resurs leverantören. |
| ResourceType | Namnet på resurs typen. |
| RestartPending | *Sant* eller *Falskt*. |
| SecurityUpdatesMissing | Antal saknade säkerhets uppdateringar som gäller.| 
| SourceComputerId | Unik identifierare för den virtuella datorn. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Unik identifierare för Azure-prenumerationen. |
| TimeGenerated | Datum och tid då posten skapades. |
| TotalUpdatesMissing | Totalt antal saknade uppdateringar som gäller. | 
| Typ | *UpdateSummary* |
| VMUUID | Unik identifierare för den virtuella datorn. |
| WindowsUpdateAgentVersion | Windows Update agentens version. |
| WindowsUpdateSetting | Visar status för Windows Update agenten. Möjliga värden:<br> *Schemalagd installation*<br> *Meddela före installation*<br> Ett fel returnerades från felaktig WUA-agent. | 
| WSUSServer | Visar fel om Windows Update Agent har problem att hjälpa med fel sökningen. |
| _ResourceId | Unik identifierare för den resurs som posten är associerad med. |

## <a name="sample-queries"></a>Exempelfrågor

I följande avsnitt finns exempel på logg frågor för uppdaterings poster som samlas in för Uppdateringshantering.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Bekräfta att icke-Azure-datorer har publicerats

Du kan kontrol lera att direktanslutna datorer kommunicerar med Azure Monitor loggar genom att köra någon av följande loggs ökningar.

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

På en Windows-dator kan du granska följande information för att verifiera agent anslutningen med Azure Monitor loggar:

1. Öppna **Microsoft Monitoring Agent**i kontroll panelen. På fliken **Azure-Log Analytics** visar agenten följande meddelande: **Microsoft Monitoring Agent har anslutit till Log Analytics**.
2. Öppna händelse loggen i Windows. Gå till **program-och tjänst loggar \ Operations Manager** och Sök efter händelse-ID 3000 och händelse-ID 5002 från käll **tjänst anslutningen**. Dessa händelser anger att datorn har registrerats med Log Analytics-arbetsytan och tar emot konfiguration.

Om agenten inte kan kommunicera med Azure Monitor loggar och agenten är konfigurerad för att kommunicera med Internet via en brand vägg eller proxyserver, kontrollerar du att brand väggen eller proxyservern har kon figurer ATS korrekt. Information om hur du verifierar att brand väggen eller proxyservern har kon figurer ATS korrekt finns i [nätverks konfiguration för Windows-agent](../azure-monitor/platform/agent-windows.md) eller [nätverks konfiguration för Linux-agenten](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Om Linux-systemen har kon figurer ATS för att kommunicera med en proxy eller Log Analytics gateway och du registrerar den här lösningen uppdaterar du *proxyn. conf* -behörighet för att ge behörigheten omiuser Group Läs behörighet för filen genom att använda följande kommandon:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nyligen tillagda Linux-agenter visar statusen **uppdaterad** när en utvärdering har utförts. Den här processen kan ta upp till 6 timmar.

För att bekräfta att en Operations Manager hanterings grupp kommunicerar med Azure Monitor loggar, se [verifiera Operations Manager integrering med Azure Monitor loggar](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Enskilda utvärderings frågor för Azure VM (Windows)

Ersätt VMUUID-värdet med VM-GUID för den virtuella dator som du frågar. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

### <a name="single-azure-vm-assessment-queries-linux"></a>Enkla Azure VM Assessment-frågor (Linux)

För vissa Linux-distributioner, finns det ett värde för [endian](https://en.wikipedia.org/wiki/Endianness) -fel med VMUUID-värdet som kommer från Azure Resource Manager och vad som lagras i Azure Monitor loggar. Följande fråga söker efter en matchning på antingen endian. Ersätt VMUUID-värdena med big-endian-och lite-endian-formatet för GUID för att returnera resultaten korrekt. Du kan hitta VMUUID som ska användas genom att köra följande fråga i Azure Monitor loggar: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

### <a name="multi-vm-assessment-queries"></a>Frågor om utvärdering av flera virtuella datorer

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

#### <a name="missing-updates-summary"></a>Sammanfattning av saknade uppdateringar

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

#### <a name="computers-list"></a>Lista med datorer

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

#### <a name="missing-updates-list"></a>Lista med uppdateringar som saknas

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

* Använd loggs ökningar i [Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade uppdaterings data.
* [Skapa aviseringar](automation-tutorial-update-management.md#configure-alerts) för status för uppdaterings distribution.
