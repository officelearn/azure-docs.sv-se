---
title: Azure Monitor loggar datamodell för Azure Backup
description: Den här artikeln berättar om Azure Monitor loggar information om modellen för Azure Backup-data.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075278"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-datamodell för Azure Backup-data

Använd Log Analytics-datamodell för att skapa anpassade aviseringar från Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Med hjälp av Azure Backup-datamodell

Du kan använda följande fält som ingår i datamodellen för att skapa visuella objekt, anpassade frågor och instrumentpanel efter behov.

### <a name="alert"></a>Varning

Den här tabellen innehåller information om aviseringen relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| AlertUniqueId_s |Text |Unik identifierare för den genererade aviseringen |
| AlertType_s |Text |Typen av avisering, till exempel säkerhetskopiering |
| AlertStatus_s |Text |Status för aviseringen, till exempel aktiv |
| AlertOccurrenceDateTime_s |Datum/tid |Datum och tid då aviseringen skapades |
| AlertSeverity_s |Text |Allvarlighetsgrad för aviseringen, till exempel kritiskt |
|AlertTimeToResolveInMinutes_s    | Tal        |Åtgången tid för att lösa en avisering. Tomt för aktiva aviseringar.         |
|AlertConsolidationStatus_s   |Text         |Identifiera om aviseringen är en sammanslagen avisering eller inte         |
|CountOfAlertsConsolidated_s     |Tal         |Antalet aviseringar som konsolideras om det är en sammanslagen avisering          |
|AlertRaisedOn_s     |Text         |Typen av enhet som varningen aktiveras på         |
|AlertCode_s     |Text         |Kod för att unikt identifiera en aviseringstyp         |
|RecommendedAction_s   |Text         |Åtgärd rekommenderas att lösa aviseringen         |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopieringsobjekt som är associerade med aviseringen |
| SchemaVersion_s |Text |Aktuella versionen av schemat, till exempel **V2** |
| State_s |Text |Aktuell status för aviseringen objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Typ av provider för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som den här aviseringen tillhör |
| OperationName |Text |Namnet på den aktuella åtgärden, till exempel avisering |
| Kategori |Text |Kategori för diagnostikdata som skickas till Azure Monitor-loggar. Alltid AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerade med aviseringen |
| VaultUniqueId_s |Text |Unik identifierare för skyddade valvet som är associerade med aviseringen |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Unik identifierare för resursen om vilka data som samlas in. Till exempel ett Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="backupitem"></a>BackupItem

Den här tabellen innehåller information om säkerhetskopiering objekt-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopierade objektet |
| BackupItemId_s |Text |Identifierare för säkerhetskopieringsobjekt |
| BackupItemName_s |Text |Namnet på säkerhetskopieringsobjektet |
| BackupItemFriendlyName_s |Text |Eget namn på säkerhetskopieringsobjekt |
| BackupItemType_s |Text |Typ av säkerhetskopieringsobjekt, till exempel VM FileFolder |
| ProtectionState_s |Text |Aktuell status för skydd av säkerhetskopieringsobjekt, till exempel skyddade, ProtectionStopped |
| ProtectionGroupName_s |Text | Namnet på Skyddsgruppen Säkerhetskopieringsobjekt är skyddad, för SC DPM och MABS, om tillämpligt|
| SecondaryBackupProtectionState_s |Text |Om sekundärt skydd är aktiverat för säkerhetskopieringsobjektet|
| SchemaVersion_s |Text |Version av schema, till exempel **V2** |
| State_s |Text |Tillståndet för säkerhetskopieringsobjekt-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Typ av provider för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som det här säkerhetskopieringsobjektet tillhör |
| OperationName |Text |Namnet på åtgärden, till exempel BackupItem |
| Kategori |Text |Kategori för diagnostikdata som skickas till Azure Monitor-loggar. Alltid AzureBackupReport |
| Resurs |Text |Resurs för vilka data som samlas in, exempelvis namn för Recovery Services-valv |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resurs-id för data som samlas in, till exempel Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (för t.ex. Recovery Services-valv) för data som samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för t.ex. Recovery Services-valv) för data som samlas in |
| ResourceProvider |Text |Resource provider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data som samlas in, till exempel valv |

### <a name="backupitemassociation"></a>BackupItemAssociation

Den här tabellen innehåller information om säkerhetskopieringsobjekt associationer med olika entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Unikt Id för säkerhetskopieringsobjektet |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V2** |
| State_s |Text |Aktuell status för kopplingsobjektet säkerhetskopieringsobjekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Front-end-storlek för säkerhetskopieringsobjektet |
| BackupManagementServerUniqueId_s |Text | Fält för att unikt identifiera hanteringsservern Säkerhetskopieringsobjekt för säkerhetskopiering skyddas via, om tillämpligt |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - BackupItemAssociation |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| PolicyUniqueId_g |Text |Unikt ID för principen som är associerade med säkerhetskopieringsobjektet |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerade med säkerhetskopieringsobjektet |
| VaultUniqueId_s |Text |Unik identifierare för det valv som innehåller säkerhetskopieringsobjektet |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (för t.ex. Recovery Services-valv) för vilka data som samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för t.ex. Recovery Services-valv) för vilka data som samlas in |
| ResourceProvider |Text |Resource provider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data som samlas in, till exempel valv |

### <a name="backupmanagementserver"></a>BackupManagementServer

Den här tabellen innehåller information om säkerhetskopieringsobjekt associationer med olika entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Namnet på servern för Säkerhetskopieringshantering        |
|AzureBackupAgentVersion_s     |Text         |Versionen av Azure Backup-agenten på servern för Säkerhetskopieringshantering          |
|BackupManagementServerVersion_s     |Text         |Version av Server för Säkerhetskopieringshantering|
|BackupManagementServerOSVersion_s    |Text            |OS-versionen av Management-servern för säkerhetskopiering|
|BackupManagementServerType_s     |Text         |Typ av Server för Säkerhetskopieringshantering som MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Fält för att unikt identifiera Backup Management Server       |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (för t.ex. Recovery Services-valv) för vilka data som samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (för t.ex. Recovery Services-valv) för vilka data som samlas in |
| ResourceProvider |Text |Resource provider för data som samlas in, till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Typ av resurs för data som samlas in, till exempel valv |

### <a name="job"></a>Jobb

Den här tabellen innehåller information om projektspecifika fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopierade objektet |
| SchemaVersion_s |Text |Version av schema, till exempel **V2** |
| State_s |Text |Aktuell status för jobbobjektet, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - jobb |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Azure Monitor-loggar, är det AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerad med jobbet |
| ProtectedContainerUniqueId_s |Text | Unikt Id för att identifiera skyddade behållaren jobbet körs på |
| VaultUniqueId_s |Text |Unik identifierare för skyddade valvet |
| JobOperation_s |Text |Åtgärden som jobbet körs exempelvis säkerhetskopiering, återställning, konfigurera säkerhetskopiering |
| JobStatus_s |Text |Status för det färdiga, exempelvis slutfört, misslyckades |
| JobFailureCode_s |Text |Felkod sträng på grund av som hände jobbfel |
| JobStartDateTime_s |Datum/tid |Datum och tid då jobbet startade körs |
| BackupStorageDestination_s |Text |Mål för lagring av säkerhetskopior, till exempel molnet, Disk  |
| AdHocOrScheduledJob_s |Text | Fältet för att ange om ett jobb är Ad Hoc eller schemalagd |
| JobDurationInSecs_s | Tal |Totala varaktighet i sekunder |
| DataTransferredInMB_s | Tal |Data som överförs i MB för det här jobbet|
| JobUniqueId_g |Text |Unikt Id för att identifiera jobbet |
| RecoveryJobDestination_s |Text | Mål för ett återställningsjobb där data återställs |
| RecoveryJobRPDateTime_s |DateTime | Datum, tid när den återställningspunkt som återställs skapades |
| RecoveryJobRPLocation_s |Text | Den plats där den återställningspunkt som återställs lagrades|
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id|
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="policy"></a>Princip

Den här tabellen innehåller information om principen-relaterade fält.

| Fält | Datatyp | Versioner som är tillämpliga | Beskrivning |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet anger aktuella versionen av schemat, är det **V2** |
| State_s |Text ||Aktuell status för princip-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text ||Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden - princip |
| Kategori |Text ||Det här fältet motsvarar kategori till diagnostikdata som skickas till Azure Monitor-loggar, är det AzureBackupReport |
| Resurs |Text ||Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| PolicyUniqueId_g |Text ||Unikt Id för att identifiera principen |
| PolicyName_s |Text ||Namnet på den princip som har definierats |
| BackupFrequency_s |Text ||Frekvens med vilken säkerhetskopieringar körs, till exempel, varje dag, varje vecka |
| BackupTimes_s |Text ||Datum och tid när säkerhetskopieringar är schemalagda |
| BackupDaysOfTheWeek_s |Text ||Dagar i veckan när säkerhetskopieringar har schemalagts |
| RetentionDuration_s |Heltal ||Kvarhållningsvaraktighetens för konfigurerade säkerhetskopieringar |
| DailyRetentionDuration_s |Heltal ||Totalt antal kvarhållning varaktighet i dagar för konfigurerade säkerhetskopieringar |
| DailyRetentionTimes_s |Text ||Datum och tid när bevarande varje dag har konfigurerats |
| WeeklyRetentionDuration_s |Decimaltal ||Total varaktighet för varje vecka kvarhållning i veckor för konfigurerade säkerhetskopieringar |
| WeeklyRetentionTimes_s |Text ||Datum och tid när kvarhållning av veckovis har konfigurerats |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dagar i veckan har valts för kvarhållning av veckovis |
| MonthlyRetentionDuration_s |Decimaltal ||Totalt antal kvarhållningsvaraktighetens i månader för konfigurerade säkerhetskopieringar |
| MonthlyRetentionTimes_s |Text ||Datum och tid när månatliga kvarhållning har konfigurerats |
| MonthlyRetentionFormat_s |Text ||Typen av konfiguration för månatliga kvarhållning, till exempel varje dag för dag-baserat, varje vecka för vecka baserat |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dagar i veckan har valts för kvarhållning av månatlig |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Veckor i månaden när månatliga kvarhållning har konfigurerats, till exempel första, sista osv. |
| YearlyRetentionDuration_s |Decimaltal ||Totalt antal kvarhållningsvaraktighetens i år för konfigurerade säkerhetskopior |
| YearlyRetentionTimes_s |Text ||Datum och tid när årliga kvarhållning har konfigurerats |
| YearlyRetentionMonthsOfTheYear_s |Text ||Månader som har valts för kvarhållning av årlig |
| YearlyRetentionFormat_s |Text ||Typen av konfiguration för årliga kvarhållning, till exempel varje dag för dag-baserat, varje vecka för vecka baserat | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datum i månaden som valts för kvarhållning av årlig |
| SynchronisationFrequencyPerDay_s |Heltal |v2|Antal gånger under en dag som en filsäkerhetskopia synkroniseras för SC DPM och MABS |
| DiffBackupFormat_s |Text |v2|Format för differentiella säkerhetskopieringar för SQL i virtuell Azure-säkerhetskopiering |
| DiffBackupTime_s |Tid |v2|Tid för differentiella säkerhetskopieringar för SQL i Azure VM Backup|
| DiffBackupRetentionDuration_s |Decimaltal |v2|Kvarhållningsvaraktighetens för differentiella säkerhetskopieringar för SQL i Azure VM Backup|
| LogBackupFrequency_s |Decimaltal |v2|Frekvens för säkerhetskopieringar för SQL|
| LogBackupRetentionDuration_s |Decimaltal |v2|Varaktighet för kvarhållning för säkerhetskopior för SQL i Azure VM Backup|
| DiffBackupDaysofTheWeek_s |Text |v2|Dagar i veckan för differentiella säkerhetskopieringar för SQL i Azure VM Backup|
| SourceSystem |Text ||Källsystemet för det aktuella - Azure |
| ResourceId |Text ||Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text ||Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text ||Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text ||Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text ||Resurstyp som data samlas in. Till exempel valv |

### <a name="policyassociation"></a>PolicyAssociation

Den här tabellen innehåller information om principkopplingar med olika entiteter.

| Fält | Datatyp | Versioner som är tillämpliga | Beskrivning |
| --- | --- | --- | --- |
| EventName_s |Text ||Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text ||Det här fältet anger aktuella versionen av schemat, är det **V2** |
| State_s |Text ||Aktuell status för princip-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text ||Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text ||Det här fältet representerar namnet på den aktuella åtgärden - PolicyAssociation |
| Kategori |Text ||Det här fältet motsvarar kategori till diagnostikdata som skickas till Azure Monitor-loggar, är det AzureBackupReport |
| Resurs |Text ||Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| PolicyUniqueId_g |Text ||Unikt Id för att identifiera principen |
| VaultUniqueId_s |Text ||Unikt Id för det valv som tillhör den här principen till |
| BackupManagementServerUniqueId_s |Text |v2 |Fält för att unikt identifiera hanteringsservern Säkerhetskopieringsobjekt för säkerhetskopiering skyddas via, om tillämpligt        |
| SourceSystem |Text ||Källsystemet för det aktuella - Azure |
| ResourceId |Text ||Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text ||Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text ||Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text ||Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text ||Resurstyp som data samlas in. Till exempel valv |

### <a name="protected-container"></a>Skyddade behållare

Den här tabellen innehåller standardfält om skyddade behållare. (Var ProtectedServer i v1)

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Fält för att unikt identifiera en skyddad behållare |
| ProtectedContainerOSType_s |Text |OS-typ för behållaren skyddade |
| ProtectedContainerOSVersion_s |Text |OS-versionen av skyddade behållaren |
| AgentVersion_s |Text |Versionsnumret för agenten säkerhetskopiering eller Skyddsagenten (när det gäller SC DPM och MABS) |
| BackupManagementType_s |Text |Typ av provider för att utföra säkerhetskopieringen exempelvis IaaSVM FileFolder |
| EntityState_s |Text |Aktuell status för skyddade server-objekt till exempel aktiv, har tagits bort |
| ProtectedContainerFriendlyName_s |Text |Eget namn på skyddad server |
| ProtectedContainerName_s |Text |Namnet på den skyddade behållaren |
| ProtectedContainerWorkloadType_s |Text |Typ av behållaren skyddade säkerhetskopieras till exempel IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Oavsett om behållaren skyddade finnas lokalt eller i Azure |
| ProtectedContainerType_s |Text |Om den skyddade behållare är en server eller en behållare |

### <a name="storage"></a>Storage

Den här tabellen innehåller information om storage-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaltal |Säkerhetskopiering molnlagring som används av säkerhetskopieringar, beräknas baserat på senaste värde |
| ProtectedInstances_s |Decimaltal |Antalet skyddade instanser som används för att beräkna frontend-lagring i fakturering, beräknad baserat på senaste värdet |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V2** |
| State_s |Text |Aktuell status för lagringsobjektet, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - lagring |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Azure Monitor-loggar, är det AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unikt Id för den skyddade servern som lagring beräknas |
| VaultUniqueId_s |Text |Unikt Id för valvet för lagring beräknas |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="storageassociation"></a>StorageAssociation

Den här tabellen innehåller grundläggande storage-relaterade fält som ansluter lagringen till andra entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Unikt Id som används för att identifiera entiteten lagring |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V2** |
| BackupItemUniqueId_s |Text |Unikt Id som används för att identifiera det säkerhetskopieringsobjekt som hör till storage-entitet |
| BackupManagementServerUniqueId_s |Text |Unikt Id som används för att identifiera server för säkerhetskopieringshantering som hör till storage-entitet|
| VaultUniqueId_s |Text |Unikt Id som används för att identifiera det valv som hör till storage-entitet|
| StorageConsumedInMBs_s |Tal|Storleken på lagringsutrymmet som förbrukas av motsvarande säkerhetskopieringsobjekt i motsvarande storage |
| StorageAllocatedInMBs_s |Tal |Mängden lagringsutrymme som allokerats av motsvarande säkerhetskopieringsobjekt i motsvarande lagringen av typen Disk|

### <a name="vault"></a>Valv

Den här tabellen innehåller information om vault-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V2** |
| State_s |Text |Aktuell status för valvobjekt, till exempel aktiv, har tagits bort |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - valvet |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Azure Monitor-loggar, är det AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| VaultUniqueId_s |Text |Unikt Id för valvet |
| VaultName_s |Text |Namnet på valvet |
| AzureDataCenter_s |Text |Datacenter där valvet finns |
| StorageReplicationType_s |Text |Typ av lagringsreplikering för valvet, till exempel GeoRedundant |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="backup-management-server"></a>Server för Säkerhetskopieringshantering

Den här tabellen innehåller standardfält om servrar för säkerhetskopiering.

|Fält  |Datatyp  | Beskrivning  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Namnet på servern för Säkerhetskopieringshantering        |
|AzureBackupAgentVersion_s     |Text         |Versionen av Azure Backup-agenten på servern för Säkerhetskopieringshantering          |
|BackupManagmentServerVersion_s     |Text         |Version av Server för Säkerhetskopieringshantering|
|BackupManagmentServerOSVersion_s     |Text            |OS-versionen av Management-servern för säkerhetskopiering|
|BackupManagementServerType_s     |Text         |Typ av Server för Säkerhetskopieringshantering som MABS, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Fält för att unikt identifiera Backup Management Server       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Den här tabellen anger workload(s) en volym som är associerad med.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| StorageUniqueId_s |Text |Unikt Id som används för att identifiera entiteten lagring |
| BackupItemType_s |Text |Arbetsbelastningar som den här volymen är önskad lagringsplats|

### <a name="protectedinstance"></a>ProtectedInstance

Den här tabellen innehåller grundläggande skyddade instanser relaterade fält.

| Fält | Datatyp |Versioner som är tillämpliga | Beskrivning |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Unikt Id som används för att identifiera säkerhetskopieringsobjektet för virtuella datorer säkerhetskopieras med DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Unikt Id som används för att identifiera skyddade behållaren för allt, men virtuella datorer säkerhetskopieras med DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Antal av skyddade instanser för den associerade säkerhetskopieringsobjekt eller skyddade behållare på det datum / tid|

### <a name="recoverypoint"></a>RecoveryPoint

Den här tabellen innehåller grundläggande recovery peka relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Unikt Id som används för att identifiera säkerhetskopieringsobjektet för virtuella datorer säkerhetskopieras med DPM, MABS|
| OldestRecoveryPointTime_s |Text |Datum-tid då den äldsta återställningspunkten för säkerhetskopieringsobjektet|
| OldestRecoveryPointLocation_s |Text |Platsen för den äldsta återställningspunkten för säkerhetskopieringsobjektet|
| LatestRecoveryPointTime_s |Text |Datum-tid för den senaste återställningspunkten för säkerhetskopieringsobjektet|
| LatestRecoveryPointLocation_s |Text |Platsen för den senaste återställningspunkten för säkerhetskopieringsobjektet|

## <a name="next-steps"></a>Nästa steg

När du granskar datamodellen kan du börja [skapar anpassade frågor](../azure-monitor/learn/tutorial-logs-dashboards.md) i Azure Monitor-loggar att skapa din egen instrumentpanel.
