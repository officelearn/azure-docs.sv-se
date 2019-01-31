---
title: Log Analytics-datamodell för Azure Backup
description: Den här artikeln handlar om Log Analytics data modellinformation för Azure Backup-data.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: 5921ca696076a16e39252a6cb3bfae98854b5a85
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299580"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-datamodell för Azure Backup-data
Använd Log Analytics-datamodell för att skapa rapporter. Med datamodellen, kan du skapa egna förfrågningar och instrumentpaneler eller anpassa Azure Backup-data, hur du vill.

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
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopieringsobjekt som är associerade med aviseringen |
| SchemaVersion_s |Text |Aktuella versionen av schemat, till exempel **V1** |
| State_s |Text |Aktuell status för aviseringen objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Typ av provider för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som den här aviseringen tillhör |
| OperationName |Text |Namnet på den aktuella åtgärden, till exempel avisering |
| Kategori |Text |Kategori för diagnostikdata som skickas till Log Analytics. Alltid AzureBackupReport |
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
| ProtectedServerName_s |Text |Namnet på skyddad server till vilken säkerhetskopieringsobjekt som hör till |
| ProtectionState_s |Text |Aktuell status för skydd av säkerhetskopieringsobjekt, till exempel skyddade, ProtectionStopped |
| SchemaVersion_s |Text |Version av schema, till exempel **V1** |
| State_s |Text |Tillståndet för säkerhetskopieringsobjekt-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Typ av provider för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som det här säkerhetskopieringsobjektet tillhör |
| OperationName |Text |Namnet på åtgärden, till exempel BackupItem |
| Kategori |Text |Kategori för diagnostikdata som skickas till Log Analytics. Alltid AzureBackupReport |
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
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för kopplingsobjektet säkerhetskopieringsobjekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - BackupItemAssociation |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
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

### <a name="job"></a>Jobb
Den här tabellen innehåller information om projektspecifika fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Händelsens namn. Alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Unik identifierare för det säkerhetskopierade objektet |
| SchemaVersion_s |Text |Version av schema, till exempel **V1** |
| State_s |Text |Aktuell status för jobbobjektet, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - jobb |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unik identifierare för den skyddade servern som är associerad med jobbet |
| VaultUniqueId_s |Text |Unik identifierare för skyddade valvet |
| JobOperation_s |Text |Åtgärden som jobbet körs exempelvis säkerhetskopiering, återställning, konfigurera säkerhetskopiering |
| JobStatus_s |Text |Status för det färdiga, exempelvis slutfört, misslyckades |
| JobFailureCode_s |Text |Felkod sträng på grund av som hände jobbfel |
| JobStartDateTime_s |Datum/tid |Datum och tid då jobbet startade körs |
| BackupStorageDestination_s |Text |Mål för lagring av säkerhetskopior, till exempel molnet, Disk  |
| JobDurationInSecs_s | Tal |Totala varaktighet i sekunder |
| DataTransferredInMB_s | Tal |Data som överförs i MB för det här jobbet|
| JobUniqueId_g |Text |Unikt Id för att identifiera jobbet |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id|
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="policy"></a>Princip
Den här tabellen innehåller information om principen-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för princip-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - princip |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| PolicyUniqueId_g |Text |Unikt Id för att identifiera principen |
| PolicyName_s |Text |Namnet på den princip som har definierats |
| BackupFrequency_s |Text |Frekvens med vilken säkerhetskopieringar körs, till exempel, varje dag, varje vecka |
| BackupTimes_s |Text |Datum och tid när säkerhetskopieringar är schemalagda |
| BackupDaysOfTheWeek_s |Text |Dagar i veckan när säkerhetskopieringar har schemalagts |
| RetentionDuration_s |Heltal |Kvarhållningsvaraktighetens för konfigurerade säkerhetskopieringar |
| DailyRetentionDuration_s |Heltal |Totalt antal kvarhållning varaktighet i dagar för konfigurerade säkerhetskopieringar |
| DailyRetentionTimes_s |Text |Datum och tid när bevarande varje dag har konfigurerats |
| WeeklyRetentionDuration_s |Decimaltal |Total varaktighet för varje vecka kvarhållning i veckor för konfigurerade säkerhetskopieringar |
| WeeklyRetentionTimes_s |Text |Datum och tid när kvarhållning av veckovis har konfigurerats |
| WeeklyRetentionDaysOfTheWeek_s |Text |Dagar i veckan har valts för kvarhållning av veckovis |
| MonthlyRetentionDuration_s |Decimaltal |Totalt antal kvarhållningsvaraktighetens i månader för konfigurerade säkerhetskopieringar |
| MonthlyRetentionTimes_s |Text |Datum och tid när månatliga kvarhållning har konfigurerats |
| MonthlyRetentionFormat_s |Text |Typen av konfiguration för månatliga kvarhållning, till exempel varje dag för dag-baserat, varje vecka för vecka baserat |
| MonthlyRetentionDaysOfTheWeek_s |Text |Dagar i veckan har valts för kvarhållning av månatlig |
| MonthlyRetentionWeeksOfTheMonth_s |Text |Veckor i månaden när månatliga kvarhållning har konfigurerats, till exempel första, sista osv. |
| YearlyRetentionDuration_s |Decimaltal |Totalt antal kvarhållningsvaraktighetens i år för konfigurerade säkerhetskopior |
| YearlyRetentionTimes_s |Text |Datum och tid när årliga kvarhållning har konfigurerats |
| YearlyRetentionMonthsOfTheYear_s |Text |Månader som har valts för kvarhållning av årlig |
| YearlyRetentionFormat_s |Text |Typen av konfiguration för årliga kvarhållning, till exempel varje dag för dag-baserat, varje vecka för vecka baserat |
| YearlyRetentionDaysOfTheMonth_s |Text |Datum i månaden som valts för kvarhållning av årlig |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="policyassociation"></a>PolicyAssociation
Den här tabellen innehåller information om principkopplingar med olika entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för princip-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - PolicyAssociation |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| PolicyUniqueId_g |Text |Unikt Id för att identifiera principen |
| VaultUniqueId_s |Text |Unikt Id för det valv som tillhör den här principen till |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="protectedserver"></a>ProtectedServer
Den här tabellen innehåller information om skyddade server-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| ProtectedServerName_s |Text |Namnet på skyddad server |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för skyddade server-objekt, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - ProtectedServer |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unikt Id för den skyddade servern |
| RegisteredContainerId_s |Text |ID för behållare som har registrerats för säkerhetskopiering |
| ProtectedServerType_s |Text |Typ av skyddad server, till exempel Windows |
| ProtectedServerFriendlyName_s |Text |Eget namn på skyddad server |
| AzureBackupAgentVersion_s |Text |Versionsnumret för Backup-Agentversion |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Den här tabellen innehåller information om skyddade servern associationer med andra entiteter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för kopplingsobjektet skyddad server, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - ProtectedServerAssociation |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unikt Id för den skyddade servern |
| VaultUniqueId_s |Text |Unikt Id för det valv som den här skyddade servern tillhör |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="storage"></a>Storage
Den här tabellen innehåller information om storage-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaltal |Säkerhetskopiering molnlagring som används av säkerhetskopieringar, beräknas baserat på senaste värde |
| ProtectedInstances_s |Decimaltal |Antalet skyddade instanser som används för att beräkna frontend-lagring i fakturering, beräknad baserat på senaste värdet |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för lagringsobjektet, till exempel aktiv, har tagits bort |
| BackupManagementType_s |Text |Providertyp för server gör säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - lagring |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
| Resurs |Text |Det här är den resurs som data samlas, den visar Recovery Services vault-namn |
| ProtectedServerUniqueId_s |Text |Unikt Id för den skyddade servern som lagring beräknas |
| VaultUniqueId_s |Text |Unikt Id för valvet för lagring beräknas |
| SourceSystem |Text |Källsystemet för det aktuella - Azure |
| ResourceId |Text |Resursidentifieraren för data som samlas in. Exempel: Recovery Services-valv resurs-id |
| SubscriptionId |Text |Prenumerations-ID för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceGroup |Text |Resursgruppen för resursen (t.ex. Recovery Services-valv) som data samlas in |
| ResourceProvider |Text |Provider för nätverksresurser som data samlas in. Till exempel Microsoft.RecoveryServices |
| ResourceType |Text |Resurstyp som data samlas in. Till exempel valv |

### <a name="vault"></a>Valv
Den här tabellen innehåller information om vault-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |Text |Det här fältet representerar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |Text |Det här fältet anger aktuella versionen av schemat, är det **V1** |
| State_s |Text |Aktuell status för valvobjekt, till exempel aktiv, har tagits bort |
| OperationName |Text |Det här fältet representerar namnet på den aktuella åtgärden - valvet |
| Kategori |Text |Det här fältet motsvarar kategori till diagnostikdata som skickas till Log Analytics, det är AzureBackupReport |
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

## <a name="next-steps"></a>Nästa steg
När du granskar datamodellen för att skapa Azure Backup-rapporter kan du börja [skapar instrumentpanelen](../azure-monitor/learn/tutorial-logs-dashboards.md) i Log Analytics.
