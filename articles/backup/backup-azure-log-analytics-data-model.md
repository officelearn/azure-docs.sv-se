---
title: Log Analytics-datamodell för Azure Backup
description: Den här artikeln handlar om logganalys data model detaljer för Azure Backup-data.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d55ec8ac4416fe0a082812584552462292b6dbb7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-datamodell för Azure Backup-data
Den här artikeln beskriver datamodellen används för att överföra rapporteringsdata till logganalys. Med den här datamodellen kan du skapa egna frågor, instrumentpaneler, och använda den i logganalys. 

## <a name="using-azure-backup-data-model"></a>Med hjälp av Azure Backup-datamodell
Du kan använda följande fält som ingår i datamodellen för att skapa visuell information, anpassade frågor och instrumentpanel enligt dina krav.

### <a name="alert"></a>Varning
Den här tabellen innehåller information om relaterade aviseringsfält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| AlertUniqueId_s |SMS |Unikt Id för den genererade aviseringen |
| AlertType_s |SMS |Typ av genererade aviseringen, till exempel säkerhetskopiering |
| AlertStatus_s |SMS |Status för aviseringen, till exempel Active |
| AlertOccurenceDateTime_s |Datum/tid |Datum och tid då aviseringen skapades |
| AlertSeverity_s |SMS |Allvarlighetsgrad för aviseringen, till exempel kritisk |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |SMS |Unikt Id för säkerhetskopiering objekt som aviseringen tillhör |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för objektet aviseringen, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som den här aviseringen tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - avisering |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade som den här aviseringen tillhör |
| VaultUniqueId_s |SMS |Unikt Id för den skyddade som den här aviseringen tillhör |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="backupitem"></a>BackupItem
Den här tabellen innehåller information om säkerhetskopiering fält som är relaterade objekt.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |SMS |Unikt Id för objektet säkerhetskopiering |
| BackupItemId_s |SMS |Säkerhetskopiera objekt-ID |
| BackupItemName_s |SMS |Namnet på Säkerhetskopiera objekt |
| BackupItemFriendlyName_s |SMS |Eget namn för Säkerhetskopiera objekt |
| BackupItemType_s |SMS |Typ av säkerhetskopiering objekt, till exempel Virtuella FileFolder |
| ProtectedServerName_s |SMS |Namnet på den skyddade servern till vilken säkerhetskopiering objektet tillhör |
| ProtectionState_s |SMS |Säkerhetskopiera objekt, till exempel skyddade, ProtectionStopped aktuella skydd tillstånd |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för objektet Säkerhetskopiera objekt, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som säkerhetskopiering artikeln tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - BackupItem |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="backupitemassociation"></a>BackupItemAssociation
Den här tabellen innehåller information om säkerhetskopiering objektet associationer med olika enheter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |  
| BackupItemUniqueId_s |SMS |Unikt Id för objektet säkerhetskopiering |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för kopplingsobjektet Säkerhetskopiera objekt, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som säkerhetskopiering artikeln tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - BackupItemAssociation |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| PolicyUniqueId_g |SMS |Unikt Id för att identifiera principen, vilket säkerhetskopiering objekt som är kopplad till |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade servern där säkerhetskopian artikeln tillhör |
| VaultUniqueId_s |SMS |Unikt Id för valvet som säkerhetskopiering artikeln tillhör |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="job"></a>Jobb
Den här tabellen innehåller information om projektspecifika fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| BackupItemUniqueId_s |SMS |Unikt Id för säkerhetskopiering objekt som det här jobbet tillhör |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för jobbobjektet, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som det här jobbet tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - jobb |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade som det här jobbet tillhör |
| VaultUniqueId_s |SMS |Unikt Id för den skyddade som det här jobbet tillhör |
| JobOperation_s |SMS |Åtgärden som jobbet körs exempelvis säkerhetskopiering, återställning, konfigurera säkerhetskopiering |
| JobStatus_s |SMS |Status för klar jobbet, exempelvis slutfört, misslyckades |
| JobFailureCode_s |SMS |Felkod sträng på grund av som skett jobbet misslyckades |
| JobStartDateTime_s |Datum/tid |Datum och tid då jobbet startade körs |
| BackupStorageDestination_s |SMS |Mål för lagring för säkerhetskopiering, till exempel moln, Disk  |
| JobDurationInSecs_s | Tal |Totalt antal jobb varaktighet i sekunder |
| DataTransferredInMB_s | Tal |Data som överförs i MB för det här jobbet|
| JobUniqueId_g |SMS |Unikt Id för att identifiera jobbet |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="policy"></a>Princip
Den här tabellen innehåller information om principen-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för principobjektet, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som principen tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - princip |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| PolicyUniqueId_g |SMS |Unikt Id för att identifiera principen |
| PolicyName_s |SMS |Namnet på den princip som har definierats |
| BackupFrequency_s |SMS |Frekvens som säkerhetskopieringar körs, till exempel, varje dag, varje vecka |
| BackupTimes_s |SMS |Datum och tid när säkerhetskopieringar är schemalagda |
| BackupDaysOfTheWeek_s |SMS |Dagar i veckan när schemalagda säkerhetskopieringar |
| RetentionDuration_s |Heltal |Kvarhållning varaktighet för konfigurerade säkerhetskopieringar |
| DailyRetentionDuration_s |Heltal |Totalt bevarande varaktighet i dagar för konfigurerade säkerhetskopieringar |
| DailyRetentionTimes_s |SMS |Datum och tid när dagliga kvarhållning konfigurerades |
| WeeklyRetentionDuration_s |Decimaltal |Total varaktighet för varje vecka kvarhållning i veckor för konfigurerade säkerhetskopieringar |
| WeeklyRetentionTimes_s |SMS |Datum och tid när varje vecka kvarhållning har konfigurerats |
| WeeklyRetentionDaysOfTheWeek_s |SMS |Dagar i veckan har valts för kvarhållning av veckovis |
| MonthlyRetentionDuration_s |Decimaltal |Totalt bevarande varaktighet i månader för konfigurerade säkerhetskopieringar |
| MonthlyRetentionTimes_s |SMS |Datum och tid när månatliga kvarhållning har konfigurerats |
| MonthlyRetentionFormat_s |SMS |Typ av konfigurationen för månatliga kvarhållning, till exempel varje dag för dag, varje vecka baserat på vecka baserat |
| MonthlyRetentionDaysOfTheWeek_s |SMS |Dagar i veckan har valts för månatliga kvarhållning |
| MonthlyRetentionWeeksOfTheMonth_s |SMS |Veckor i månaden när månatliga kvarhållning har konfigurerats, till exempel första, sista osv. |
| YearlyRetentionDuration_s |Decimaltal |Totalt bevarande varaktighet i år för konfigurerade säkerhetskopieringar |
| YearlyRetentionTimes_s |SMS |Datum och tid när årlig kvarhållning har konfigurerats |
| YearlyRetentionMonthsOfTheYear_s |SMS |Månader som har valts för kvarhållning av årlig |
| YearlyRetentionFormat_s |SMS |Typ av konfigurationen för årlig kvarhållning, till exempel varje dag för dag, varje vecka baserat på vecka baserat |
| YearlyRetentionDaysOfTheMonth_s |SMS |Datum i månaden som valts för kvarhållning av årlig |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="policyassociation"></a>PolicyAssociation
Den här tabellen innehåller information om principkopplingar med olika enheter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för principobjektet, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen exempelvis IaaSVM FileFolder som principen tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - PolicyAssociation |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| PolicyUniqueId_g |SMS |Unikt Id för att identifiera principen |
| VaultUniqueId_s |SMS |Unikt Id för valvet som principen tillhör |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="protectedserver"></a>ProtectedServer
Den här tabellen innehåller information om skyddade server-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| ProtectedServerName_s |SMS |Namnet på skyddad server |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för skyddade server-objekt, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen exempelvis IaaSVM FileFolder som den här skyddade servern tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - ProtectedServer |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade servern |
| RegisteredContainerId_s |SMS |ID: t för behållaren som registrerats för säkerhetskopiering |
| ProtectedServerType_s |SMS |Typ av skyddade server säkerhetskopieras till exempel Windows |
| ProtectedServerFriendlyName_s |SMS |Eget namn på skyddad server |
| AzureBackupAgentVersion_s |SMS |Versionsnumret för agenten säkerhetskopiering Version |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Den här tabellen innehåller information om skyddade servern associationer till andra enheter.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för kopplingsobjektet skyddad server, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som den här skyddade servern tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - ProtectedServerAssociation |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade servern |
| VaultUniqueId_s |SMS |Unikt Id för valvet som den här skyddade servern tillhör |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

### <a name="storage"></a>Storage
Den här tabellen innehåller information om lagringsrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaltal |Säkerhetskopiering molnlagring som används av säkerhetskopior, beräknas baserat på senaste värde |
| ProtectedInstances_s |Decimaltal |Antalet skyddade instanser som används för att beräkna klientdel lagring i fakturering, beräknad baserat på senaste värdet |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för lagringsobjektet, till exempel Active, tagits bort |
| BackupManagementType_s |SMS |Providertyp för att utföra säkerhetskopieringen, till exempel IaaSVM FileFolder som lagringen tillhör |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - lagring |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| ProtectedServerUniqueId_s |SMS |Unikt Id för den skyddade servern som lagring beräknas |
| VaultUniqueId_s |SMS |Unikt Id för valvet för lagring beräknas |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet representse typ av resurs som data samlas - valv |

### <a name="vault"></a>Valv
Den här tabellen innehåller information om valvet-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| EventName_s |SMS |Det här fältet motsvarar namnet på den här händelsen, är det alltid AzureBackupCentralReport |
| SchemaVersion_s |SMS |Det här fältet anger nuvarande version av schemat, är det **V1** |
| State_s |SMS |Aktuell status för objektet valvet, till exempel Active, tagits bort |
| OperationName |SMS |Det här fältet motsvarar namnet på den aktuella åtgärden - valvet |
| Kategori |SMS |Det här fältet motsvarar datakategori diagnostik pushas till logganalys, är det AzureBackupReport |
| Resurs |SMS |Detta är den resurs som data samlas, den visar namn på Recovery Services-valvet |
| VaultUniqueId_s |SMS |Unikt Id för valvet |
| VaultName_s |SMS |Namnet på valvet |
| AzureDataCenter_s |SMS |Datacenter där valvet finns |
| StorageReplicationType_s |SMS |Typ av lagringsreplikering för valvet, till exempel GeoRedundant |
| SourceSystem |SMS |Källsystemet för det aktuella – Azure |
| Resurs-ID |SMS |Det här fältet representerar resurs-id som data samlas, den visar Recovery Services-valvet resurs-id |
| Prenumerations-ID |SMS |Det här fältet motsvarar prenumerations-id för resursen (RS valvet) som data samlas |
| Resursgrupp |SMS |Det här fältet motsvarar resursgruppen för resursen (RS valvet) som data samlas |
| ResourceProvider |SMS |Det här fältet motsvarar den resursprovidern som data samlas - Microsoft.RecoveryServices |
| ResourceType |SMS |Det här fältet motsvarar den resurstyp som data samlas - valv |

## <a name="next-steps"></a>Nästa steg
När du granskar datamodellen för att skapa rapporter för Azure Backup kan du börja [skapar instrumentpanelen](../log-analytics/log-analytics-dashboards.md) i logganalys.
