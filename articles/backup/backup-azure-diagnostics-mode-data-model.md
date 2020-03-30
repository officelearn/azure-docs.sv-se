---
title: Datamodell för Azure Monitor loggar
description: I den här artikeln kan du läsa mer om datamodellinformation för Azure Monitor Log Analytics för Azure Backup-data.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586384"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Logganalysdatamodell för Azure Backup-data

Använd datamodellen Log Analytics för att skapa anpassade aviseringar från Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Den här datamodellen refererar till Azure Diagnostics Mode för att skicka diagnostikhändelser till Log Analytics (LA). Om du vill lära dig datamodellen för det nya resursspecifika läget kan du läsa följande artikel: [Datamodell för Azure Backup Diagnostic Events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Använda Azure Backup-datamodell

Du kan använda följande fält som tillhandahålls som en del av datamodellen för att skapa visuella objekt, anpassade frågor och instrumentpanel enligt dina krav.

### <a name="alert"></a>Varning

Den här tabellen innehåller information om aviseringsrelaterade fält.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| AlertUniqueId_s |Text |Unik identifierare för den genererade aviseringen |
| AlertType_s |Text |Typ av avisering, till exempel Säkerhetskopiering |
| AlertStatus_s |Text |Status för aviseringen, till exempel Aktiv |
| AlertOccurrenceDateTime_s |Date/Time |Datum och tid då aviseringen skapades |
| AlertSeverity_s |Text |Allvarlighetsgrad för aviseringen, till exempel Kritisk |
|AlertTimeToResolveInMinutes_s    | Tal        |Det tar tid att lösa en avisering. Tom för aktiva aviseringar.         |
|AlertConsolidationStatus_s   |Text         |Identifiera om aviseringen är en konsoliderad avisering eller inte         |
|CountOfAlertsConsolidated_s     |Tal         |Antal aviseringar som konsoliderats om det är en konsoliderad avisering          |
|AlertRaisedOn_s     |Text         |Typ av enhet som aviseringen höjs på         |
|AlertCode_s     |Text         |Kod för att unikt identifiera en aviseringstyp         |
|RecommendedAction_s   |Text         |Åtgärd rekommenderas för att lösa aviseringen         |
| EventName_s |Text |Namnet på händelsen. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopieringsobjekt som är associerat med aviseringen |
| SchemaVersion_s |Text |Aktuell version av schemat, till exempel **V2** |
| State_s |Text |Aktuellt tillstånd för varningsobjektet, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text |Providertyp för säkerhetskopiering, till exempel IaaSVM, FileFolder som den här aviseringen tillhör |
| OperationName |Text |Namnet på den aktuella åtgärden, till exempel Avisering |
| Kategori |Text |Kategori av diagnostikdata som skjuts till Azure Monitor-loggar. Alltid AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| ProtectedContainerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerad med aviseringen (ProtectedServerUniqueId_s i V1)|
| VaultUniqueId_s |Text |Unik identifierare för det skyddade valvet som är associerat med aviseringen |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Unik identifierare för den resurs som data samlas in om. Ett resurs-ID för Återställningstjänster |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp för vilken data samlas in. Till exempel valv |

### <a name="backupitem"></a>BackupItem

Den här tabellen innehåller information om objektrelaterade fält för säkerhetskopiering.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Namnet på händelsen. Alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unik identifierare för säkerhetskopieringsobjektet |
| BackupItemId_s |Text |Identifierare för säkerhetskopieringsobjekt (Det här fältet är endast för v1-schema) |
| BackupItemName_s |Text |Namn på säkerhetskopieringsobjekt |
| BackupItemFriendlyName_s |Text |Eget namn på säkerhetskopieringsobjekt |
| BackupItemType_s |Text |Typ av säkerhetskopieringsobjekt, till exempel VM, FileFolder |
| BackupItemProtectionState_s |Text |Säkerhetstillstånd för säkerhetskopieringsobjektet |
| BackupItemAppVersion_s |Text |Programversion av säkerhetskopieringsobjektet |
| ProtectionState_s |Text |Det aktuella skyddstillståndet för säkerhetskopieringsobjektet, till exempel Skyddat, ProtectionStopped |
| ProtectionGroupName_s |Text | Namn på den skyddsgrupp som säkerhetsobjektet är skyddat i, för SC DPM och MABS, om tillämpligt|
| SecondaryBackupProtectionState_s |Text |Om sekundärt skydd är aktiverat för säkerhetskopieringsobjektet|
| SchemaVersion_s |Text |Version av schemat, till exempel **V2** |
| State_s |Text |Tillståndet för objektet för säkerhetskopiering, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text |Providertyp för säkerhetskopiering, till exempel IaaSVM, FileFolder som det här säkerhetskopieringsobjektet tillhör |
| OperationName |Text |Namnet på åtgärden, till exempel BackupItem |
| Kategori |Text |Kategori av diagnostikdata som skjuts till Azure Monitor-loggar. Alltid AzureBackupReport |
| Resurs |Text |Resurs för vilken data samlas in, till exempel Recovery Services-valvnamn |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resurs-ID för data som samlas in, till exempel Recovery Services-valvresurs-ID |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (för ex. Recovery Services valv) för data som samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för ex. Recovery Services valv) för data som samlas in |
| ResourceProvider |Text |Resursprovider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data som samlas in, till exempel Arkiv |

### <a name="backupitemassociation"></a>BackupItemAssociation

Den här tabellen innehåller information om säkerhetskopior av objekt med olika entiteter.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, det är alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikt ID för säkerhetskopieringsobjektet |
| SchemaVersion_s |Text |Det här fältet betecknar aktuell version av schemat, det är **V2** |
| State_s |Text |Aktuellt tillstånd för associationsobjektet för säkerhetskopieringsobjektet, till exempel Active, Deleted |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Frontend-storlek för säkerhetskopieringsobjektet |
| BackupManagementServerUniqueId_s |Text | Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt |
| Kategori |Text |Det här fältet representerar kategorin diagnostikdata som överförs till Log Analytics, det är AzureBackupReport |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - BackupItemAssociation |
| Resurs |Text |Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| ProtectedContainerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerad med säkerhetskopieringsobjektet (ProtectedServerUniqueId_s i V1) |
| VaultUniqueId_s |Text |Unik identifierare för valvet som innehåller säkerhetskopian |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (för ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data samlas in, till exempel Valv |

### <a name="backupmanagementserver"></a>BackupManagementServer

Den här tabellen innehåller information om säkerhetskopior av objekt med olika entiteter.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Namn på säkerhetskopieringshanteringsservern        |
|AzureBackupAgentVersion_s     |Text         |Version av Azure Backup Agent på säkerhetskopieringshanteringsservern          |
|BackupManagementServerVersion_s     |Text         |Version av säkerhetskopieringshanteringsservern|
|BackupManagementServerOSVersion_s    |Text            |OS-version av säkerhetskopieringshanteringsservern|
|BackupManagementServerType_s     |Text         |Typ av säkerhetskopieringshanteringsserver, som MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Fält för att unikt identifiera säkerhetskopieringshanteringsservern       |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (för ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data samlas in, till exempel Valv |

### <a name="job"></a>Jobb

Den här tabellen innehåller information om jobbrelaterade fält.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Namnet på händelsen. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för säkerhetskopieringsobjektet |
| SchemaVersion_s |Text |Version av schemat, till exempel **V2** |
| State_s |Text |Aktuellt tillstånd för jobbobjektet, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella operationen - Jobb |
| Kategori |Text |Det här fältet representerar kategorin diagnostikdata som överförs till Azure Monitor-loggar, det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som associerade jobbet |
| ProtectedContainerUniqueId_s |Text | Unikt ID för att identifiera den skyddade behållaren som jobbet körs på |
| VaultUniqueId_s |Text |Unik identifierare för det skyddade valvet |
| JobOperation_s |Text |Åtgärd för vilket jobb körs, till exempel Säkerhetskopiering, Återställning, Konfigurera säkerhetskopiering |
| JobStatus_s |Text |Status för det färdiga jobbet, till exempel Slutförd, Misslyckades |
| JobFailureCode_s |Text |Felkodsträngen på grund av vilket jobbfel inträffade |
| JobStartDateTime_s |Date/Time |Datum och tid när jobbet började köras |
| BackupStorageDestination_s |Text |Destination för lagring av säkerhetskopior, till exempel Moln, Disk  |
| AdHocOrScheduledJob_s |Text | Fält för att ange om jobbet är Ad Hoc eller Schemalagt |
| JobDurationInSecs_s | Tal |Total jobblängd i sekunder |
| DataTransferredInMB_s | Tal |Data som överförs i MB för det här jobbet|
| JobUniqueId_g |Text |Unikt ID för att identifiera jobbet |
| RecoveryJobDestination_s |Text | Destination för ett återställningsjobb, där data återställs |
| RecoveryJobRPDateTime_s |DateTime | Datumet, Tid då återställningspunkten som återställs skapades skapades |
| RecoveryJobRPLocation_s |Text | Platsen där återställningspunkten som återvinns lagrades|
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID|
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp för vilken data samlas in. Till exempel valv |

### <a name="policy"></a>Princip

Den här tabellen innehåller information om principrelaterade fält.

| Field | Datatyp | Versioner gäller | Beskrivning |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen, det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet betecknar aktuell version av schemat, det är **V2** |
| State_s |Text ||Principobjektets aktuella tillstånd, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text ||Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden - Princip |
| Kategori |Text ||Det här fältet representerar kategorin diagnostikdata som överförs till Azure Monitor-loggar, det är AzureBackupReport |
| Resurs |Text ||Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| PolicyUniqueId_g |Text ||Unikt ID för att identifiera principen |
| PolicyName_s |Text ||Namnet på den definierade principen |
| BackupFrequency_s |Text ||Frekvens med vilken säkerhetskopior körs, till exempel dagligen, veckovis |
| BackupTimes_s |Text ||Datum och tid då säkerhetskopieringar schemaläggs |
| BackupDaysOfTheWeek_s |Text ||Dagar i veckan när säkerhetskopieringar har schemalagts |
| RetentionDuration_s |Heltal ||Kvarhållningslängd för konfigurerade säkerhetskopior |
| DailyRetentionDuration_s |Heltal ||Total kvarhållningstid i dagar för konfigurerade säkerhetskopior |
| DailyRetentionTimes_s |Text ||Datum och tid då daglig kvarhållning har konfigurerats |
| WeeklyRetentionDuration_s |Decimaltal ||Total veckokvarhållningslängd i veckor för konfigurerade säkerhetskopior |
| WeeklyRetentionTimes_s |Text ||Datum och tid då veckokvarhållning har konfigurerats |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Veckodagar som valts ut för kvarhållning varje vecka |
| MonthlyRetentionDuration_s |Decimaltal ||Total kvarhållningstid i månader för konfigurerade säkerhetskopior |
| MonthlyRetentionTimes_s |Text ||Datum och tid då månatlig kvarhållning har konfigurerats |
| MonthlyRetentionFormat_s |Text ||Typ av konfiguration för månatlig kvarhållning, till exempel dagligen för dagbaserad, veckovis för veckobaserad |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Veckodagar som valts ut för månatlig kvarhållning |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Veckor i månaden när månatlig kvarhållning konfigureras, till exempel Först, Sist etc. |
| YearlyRetentionDuration_s |Decimaltal ||Total kvarhållningstid i år för konfigurerade säkerhetskopieringar |
| YearlyRetentionTimes_s |Text ||Datum och tid då årlig kvarhållning har konfigurerats |
| YearlyRetentionMonthsOfTheYear_s |Text ||Månader av året som valts för årlig retention |
| YearlyRetentionFormat_s |Text ||Typ av konfiguration för årlig kvarhållning, till exempel dagligen för dagbaserad, veckovis för veckobaserad | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datum för den månad som valts för årlig kvarhållning |
| SynchronisationFrequencyPerDay_s |Heltal |v2|Antal gånger under en dag som en säkerhetskopiering av filer synkroniseras för SC DPM och MABS |
| DiffBackupFormat_s |Text |v2|Format för differentiella säkerhetskopior för SQL i Azure VM-säkerhetskopiering |
| DiffBackupTime_s |Tid |v2|Tid för differentiella säkerhetskopieringar för SQL i Azure VM Backup|
| DiffBackupRetentionDuration_s |Decimaltal |v2|Kvarhållningslängd för differentiella säkerhetskopieringar för SQL i Azure VM Backup|
| LogBackupFrequency_s |Decimaltal |v2|Frekvens för log-säkerhetskopior för SQL|
| LogBackupRetentionDuration_s |Decimaltal |v2|Kvarhållningslängd för loggsäkerhetskopior för SQL i Azure VM Backup|
| DiffBackupDaysofTheWeek_s |Text |v2|Veckodagar för differentiella säkerhetskopior för SQL i Azure VM Backup|
| SourceSystem |Text ||Källsystem för aktuella data - Azure |
| ResourceId |Text ||Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text ||Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text ||Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text ||Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text ||Resurstyp för vilken data samlas in. Till exempel valv |

### <a name="policyassociation"></a>PolicyAssociation

Den här tabellen innehåller information om principassociationer med olika entiteter.

| Field | Datatyp | Versioner gäller | Beskrivning |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen, det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet betecknar aktuell version av schemat, det är **V2** |
| State_s |Text ||Principobjektets aktuella tillstånd, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text ||Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden - PolicyAssociation |
| Kategori |Text ||Det här fältet representerar kategorin diagnostikdata som överförs till Azure Monitor-loggar, det är AzureBackupReport |
| Resurs |Text ||Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| PolicyUniqueId_g |Text ||Unikt ID för att identifiera principen |
| VaultUniqueId_s |Text ||Unikt ID för valvet som den här principen tillhör |
| BackupManagementServerUniqueId_s |Text |v2 |Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt        |
| SourceSystem |Text ||Källsystem för aktuella data - Azure |
| ResourceId |Text ||Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text ||Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text ||Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text ||Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text ||Resurstyp för vilken data samlas in. Till exempel valv |

### <a name="protected-container"></a>Skyddad behållare

Den här tabellen innehåller grundläggande fält om skyddade behållare. (Var ProtectedServer i v1)

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Fält för att unikt identifiera en skyddad behållare |
| ProtectedContainerOSType_s |Text |OS-typ för den skyddade behållaren |
| ProtectedContainerOSVersion_s |Text |OS-version av den skyddade behållaren |
| AgentVersion_s |Text |Versionsnummer för Agent Backup eller Protection Agent (Vid SC DPM och MABS) |
| BackupManagementType_s |Text |Providertyp för säkerhetskopiering. Till exempel IaaSVM, FileFolder |
| EntityState_s |Text |Det aktuella tillståndet för det skyddade serverobjektet. Till exempel Aktiv, Borttagen |
| ProtectedContainerFriendlyName_s |Text |Eget namn på skyddad server |
| ProtectedContainerName_s |Text |Namn på den skyddade behållaren |
| ProtectedContainerWorkloadType_s |Text |Typ av den skyddade behållaren som säkerhetskopieras. Till exempel, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Om den skyddade behållaren finns lokalt eller i Azure |
| ProtectedContainerType_s |Text |Om den skyddade behållaren är en server eller en behållare |
| ProtectedContainerProtectionState_s"  |Text |Skyddstillstånd för den skyddade behållaren |

### <a name="storage"></a>Lagring

Den här tabellen innehåller information om lagringsrelaterade fält.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaltal |Molnsäkerhetskopieringslagring som används av säkerhetskopior, beräknat baserat på senaste värde (Det här fältet är endast för v1-schema)|
| ProtectedInstances_s |Decimaltal |Antal skyddade instanser som används för beräkning av klientdelslagring i fakturering, beräknat baserat på det senaste värdet |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet betecknar aktuell version av schemat, det är **V2** |
| State_s |Text |Lagringsobjektets aktuella tillstånd, till exempel Aktiv, Borttagen |
| BackupManagementType_s |Text |Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - Lagring |
| Kategori |Text |Det här fältet representerar kategorin diagnostikdata som överförs till Azure Monitor-loggar, det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| ProtectedServerUniqueId_s |Text |Unikt ID för den skyddade server för vilken lagring beräknas |
| VaultUniqueId_s |Text |Unikt ID för valvet för lagring beräknas |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp för vilken data samlas in. Till exempel valv |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagringsentiteten |
| StorageType_s |Text |Typ av lagring, till exempel Moln, Volym, Disk |
| StorageName_s |Text |Namn på lagringsenhet, till exempel E:\ |
| StorageTotalSizeInGBs_s |Text |Total lagringsstorlek, i GB, förbrukad av lagringsenhet|

### <a name="storageassociation"></a>LagringAssociation

Den här tabellen innehåller grundläggande lagringsrelaterade fält som ansluter lagring till andra entiteter.

| Field | Datatyp | Beskrivning |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagringsentiteten |
| SchemaVersion_s |Text |Det här fältet betecknar aktuell version av schemat, det är **V2** |
| BackupItemUniqueId_s |Text |Unikt ID som används för att identifiera säkerhetskopieringsobjektet som är relaterat till lagringsentiteten |
| BackupManagementServerUniqueId_s |Text |Unikt ID som används för att identifiera hanteringsservern för säkerhetskopiering relaterad till lagringsentiteten|
| VaultUniqueId_s |Text |Unikt ID som används för att identifiera valvet som är relaterat till lagringsentiteten|
| StorageConsumedInMBs_s |Tal|Lagringsstorlek som förbrukas av motsvarande säkerhetskopieringsobjekt i motsvarande lagring |
| StorageAllocatedInMBs_s |Tal |Lagringsstorlek som allokerats av motsvarande säkerhetskopieringsobjekt i motsvarande lagring av typen Disk|

### <a name="vault"></a>Valv

Den här tabellen innehåller information om arkivrelaterade fält.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, det är alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet betecknar aktuell version av schemat, det är **V2** |
| State_s |Text |Valvobjektets aktuella tillstånd, till exempel Aktiv, Borttagen |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - Arkiv |
| Kategori |Text |Det här fältet representerar kategorin diagnostikdata som överförs till Azure Monitor-loggar, det är AzureBackupReport |
| Resurs |Text |Detta är den resurs för vilken data samlas in, visar det Recovery Services valvnamn |
| VaultUniqueId_s |Text |Unikt ID för valvet |
| VaultName_s |Text |Namn på valvet |
| AzureDataCenter_s |Text |Datacenter där valvet finns |
| StorageReplicationType_s |Text |Typ av lagringsreplikering för valvet, till exempel GeoRedundant |
| SourceSystem |Text |Källsystem för aktuella data - Azure |
| ResourceId |Text |Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| SubscriptionId |Text |Prenumerationsidentifierare för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services valv) för vilka data samlas in |
| ResourceProvider |Text |Resursprovider för vilken data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp för vilken data samlas in. Till exempel valv |

### <a name="backup-management-server"></a>Server för säkerhetskopieringshantering

Den här tabellen innehåller grundläggande fält om servrar för säkerhetskopieringshantering.

|Field  |Datatyp  | Beskrivning  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Namn på säkerhetskopieringshanteringsservern        |
|AzureBackupAgentVersion_s     |Text         |Version av Azure Backup Agent på säkerhetskopieringshanteringsservern          |
|BackupManagementServerVersion_s     |Text         |Version av säkerhetskopieringshanteringsservern|
|BackupManagementServerOSVersion_s     |Text            |OS-version av säkerhetskopieringshanteringsservern|
|BackupManagementServerType_s     |Text         |Typ av säkerhetskopieringshanteringsserver, som MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Fält för att unikt identifiera säkerhetskopieringshanteringsservern       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Den här tabellen anger vilka arbetsbelastningar en volym är associerad med.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| StorageUniqueId_s |Text |Unikt ID som används för att identifiera lagringsentiteten |
| BackupItemType_s |Text |De arbetsbelastningar för vilka den här volymen är den önskade|

### <a name="protectedinstance"></a>ProtectedInstance

Den här tabellen innehåller grundläggande skyddade instanser relaterade fält.

| Field | Datatyp |Versioner gäller | Beskrivning |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Unikt ID som används för att identifiera säkerhetskopieringsobjektet för virtuella datorer som säkerhetskopierats med DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Unikt ID som används för att identifiera den skyddade behållaren för allt utom virtuella datorer som säkerhetskopierats med DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Antal skyddade instanser för det associerade säkerhetskopieringsobjektet eller den skyddade behållaren vid den datumtiden|

### <a name="recoverypoint"></a>Återställningspunkt

Den här tabellen innehåller grundläggande återställningspunktsrelaterade fält.

| Field | Datatyp | Beskrivning |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Unikt ID som används för att identifiera säkerhetskopieringsobjektet för virtuella datorer som säkerhetskopierats med DPM, MABS|
| OldestRecoveryPointTime_s |Text |Datumtid för den äldsta återställningspunkten för säkerhetskopieringsobjektet|
| OldestRecoveryPointLocation_s |Text |Platsen för den äldsta återställningspunkten för säkerhetskopieringsobjektet|
| LatestRecoveryPointTime_s |Text |Datum för den senaste återställningspunkten för säkerhetskopieringsobjektet|
| LatestRecoveryPointLocation_s |Text |Platsen för den senaste återställningspunkten för säkerhetskopieringsobjektet|

## <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Nedan följer några exempel som hjälper dig att skriva frågor om Azure Backup-data som finns i tabellen Azure Diagnostics:

- Alla lyckade säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Alla misslyckade säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Alla lyckade azure vm-säkerhetskopieringsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alla lyckade SQL-loggsäkerhetsjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alla lyckade Azure Backup-agentjobb

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Nästa steg

När du har granskat datamodellen kan du börja [skapa anpassade frågor](../azure-monitor/learn/tutorial-logs-dashboards.md) i Azure Monitor-loggar för att skapa en egen instrumentpanel.
