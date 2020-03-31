---
title: Datamodell för diagnostikhändelser för Azure Backup
description: Den här datamodellen refererar till resursspecifikt läge för att skicka diagnostikhändelser till Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583392"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Datamodell för azure backup diagnostikhändelser

## <a name="coreazurebackup"></a>CoreAzureBackup

Den här tabellen innehåller information om grundläggande säkerhetskopieringsenheter, till exempel valv och säkerhetskopieringsobjekt.

| **Field**                         | **Datatyp** | **Beskrivning**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID. |
| OperationName                     | Text          | Det här fältet representerar namnet på den aktuella åtgärden - BackupItem, BackupItemAssociation eller ProtectedContainer. |
| Kategori                          | Text          | Det här fältet representerar kategorin diagnostikdata som skjuts till Azure Monitor-loggar. Till exempel CoreAzureBackup. |
| AgentVersion (AgentVersion)                      | Text          | Versionsnummer för Agent Backup eller Protection Agent (vid SC DPM och MABS) |
| AzureBackupAgentVersion           | Text          | Version av Azure Backup Agent på säkerhetskopieringshanteringsservern |
| AzureDataCenter                   | Text          | Datacenter där valvet finns                       |
| BackupItemAppVersion              | Text          | Programversion av säkerhetskopieringsobjektet                       |
| BackupItemFriendlyName            | Text          | Eget namn på säkerhetskopian                             |
| BackupItemName                    | Text          | Namn på säkerhetskopian                                      |
| BackupItemProtectionState         | Text          | Säkerhetstillstånd för säkerhetskopieringsobjektet                          |
| BackupItemFrontEndSize            | Text          | Frontend-storlek för säkerhetskopieringsobjektet                            |
| BackupItemType                    | Text          | Typ av säkerhetskopieringsobjekt. Till exempel: VM, FileFolder             |
| BackupItemUniqueId                | Text          | Unik identifierare för säkerhetskopieringsobjektet                         |
| BackupManagementServerTyp        | Text          | Typ av säkerhetskopieringshanteringsserver, som i MABS, SC DPM     |
| BackupManagementServerUniqueId    | Text          | Fält för att unikt identifiera säkerhetskopieringshanteringsservern      |
| BackupManagementType              | Text          | Providertyp för servern som utför säkerhetskopieringsjobb. Till exempel IaaSVM, FileFolder |
| BackupManagementServerName        | Text          | Namn på säkerhetskopieringshanteringsservern                         |
| BackupManagementServerOSVersion   | Text          | OS-version av säkerhetskopieringshanteringsservern                   |
| BackupManagementServerVersion     | Text          | Version av säkerhetskopieringshanteringsservern                      |
| SenasteRecoveryPointLocation       | Text          | Platsen för den senaste återställningspunkten för säkerhetskopieringsobjektet    |
| Senaste Nytt av detta           | DateTime      | Datum för den senaste återställningspunkten för säkerhetskopieringsobjektet   |
| ÄldstaRecoveryPointLocation       | Text          | Platsen för den äldsta återställningspunkten för säkerhetskopieringsobjektet    |
| ÄldstaRecoveryPointTime           | DateTime      | Datum för den senaste återställningspunkten för säkerhetskopieringsobjektet   |
| PolicyUniqueId                    | Text          | Unikt ID för att identifiera principen                             |
| ProtectedContainerFriendlyName    | Text          | Eget namn på den skyddade servern                        |
| SkyddadcontainerLokalisering        | Text          | Om den skyddade behållaren finns lokalt eller i Azure |
| ProtectedContainerName            | Text          | Namn på den skyddade behållaren                            |
| ProtectedContainerOSType          | Text          | OS-typ för den skyddade behållaren                          |
| ProtectedContainerOSVersion       | Text          | OS-version av den skyddade behållaren                        |
| ProtectedContainerProtectionState | Text          | Skyddstillstånd för den skyddade behållaren                  |
| SkyddadcontainerTyp            | Text          | Om den skyddade behållaren är en server eller en behållare  |
| ProtectedContainerUniqueId        | Text          | Unikt ID som används för att identifiera den skyddade behållaren för allt utom virtuella datorer som säkerhetskopierats med DPM, MABS |
| ProtectedContainerWorkloadType    | Text          | Typ av den skyddade behållaren som säkerhetskopieras. Till exempel, IaaSVMContainer |
| SkyddsgruppNamn               | Text          | Namn på den skyddsgrupp som säkerhetsobjektet är skyddat i, för SC DPM och MABS, om tillämpligt |
| ResourceGroupName                 | Text          | Resursgruppen för resursen (till exempel Recovery Services-valvet) för data som samlas in |
| SchemaVersion                     | Text          | Det här fältet betecknar den aktuella versionen av schemat, det är **V2** |
| SecondaryBackupProtectionState    | Text          | Om sekundärt skydd är aktiverat för säkerhetskopieringsobjektet  |
| Status                             | Text          | Tillståndet för objektet för säkerhetskopieringsobjektet. Till exempel Aktiv, Borttagen |
| StorageReplicationType            | Text          | Typ av lagringsreplikering för valvet. Till exempel georedundant |
| SubscriptionId                    | Text          | Prenumerationsidentifierare för den resurs (till exempel Recovery Services-valvet) för vilken data samlas in |
| VaultName (valvNamn)                         | Text          | Namn på valvet                                            |
| ArkivTaggar                         | Text          | Taggar som är associerade med arkivresursen                    |
| VaultUniqueId                     | Text          | Unik identifierare för valvet                             |
| SourceSystem                      | Text          | Källsystem för aktuella data - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Den här tabellen innehåller information om aviseringsrelaterade fält.

| **Field**                      | **Datatyp** | **Beskrivning**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Unik identifierare för den resurs som data samlas in om. Ett resurs-ID för Återställningstjänster |
| OperationName                  | Text          | Namnet på den aktuella åtgärden. Till exempel Alert            |
| Kategori                       | Text          | Kategori av diagnostikdata som skjuts till Azure Monitor-loggar - AddonAzureBackupAlerts |
| Varningskod                      | Text          | Kod för att unikt identifiera en aviseringstyp                     |
| Status för alertkonsolidation       | Text          | Identifiera om aviseringen är en konsoliderad avisering eller inte         |
| AlertOccurrenceDateTime        | DateTime      | Datum och tid då aviseringen skapades                     |
| AlertRaisedOn                  | Text          | Typ av enhet som aviseringen höjs på                        |
| AlertSeverity                  | Text          | Allvarlighetsgrad för aviseringen. Till exempel kritiska                 |
| Status för aviseringar                    | Text          | Status för aviseringen. Till exempel kan Active                     |
| AlertTimeToResolveInMinutes    | Tal        | Det tar tid att lösa en avisering. Tom för aktiva aviseringar.     |
| AlertType (Aviseringstyp)                      | Text          | Typ av avisering. Till exempel backup                           |
| AlertUniqueId                  | Text          | Unik identifierare för den genererade aviseringen                    |
| BackupItemUniqueId             | Text          | Unik identifierare för det säkerhetskopieringsobjekt som är associerat med aviseringen |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| CountOfAlertsKonsoliderat      | Tal        | Antal aviseringar som konsoliderats om det är en konsoliderad avisering  |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade servern som är associerad med aviseringen |
| Rekommenderad åtgärd              | Text          | Åtgärd rekommenderas för att lösa aviseringen                      |
| SchemaVersion                  | Text          | Aktuell version av schemat, till exempel **V2**            |
| Status                          | Text          | Aktuellt tillstånd för varningsobjektet, till exempel Aktiv, Borttagen |
| LagringUniqueId                | Text          | Unikt ID som används för att identifiera lagringsentiteten                |
| VaultUniqueId                  | Text          | Unikt ID som används för att identifiera valvet som är relaterat till aviseringen    |
| SourceSystem                   | Text          | Källsystem för aktuella data - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupSkyddadeInstance

Den här tabellen innehåller grundläggande skyddade instanser relaterade fält.

| **Field**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Unik identifierare för den resurs som data samlas in om. Ett resurs-ID för Återställningstjänster |
| OperationName                  | Text          | Namnet på åtgärden, till exempel ProtectedInstance         |
| Kategori                       | Text          | Kategori av diagnostikdata som skjuts till Azure Monitor-loggar - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | Unikt ID för säkerhetskopieringsobjektet                                 |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för server som utför säkerhetskopieringsjobb, till exempel IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Text          | Unikt ID för att identifiera den skyddade behållaren som jobbet körs på |
| ProtectedInstanceCount         | Text          | Antal skyddade instanser för det associerade säkerhetskopieringsobjektet eller den skyddade behållaren vid den datumtiden |
| SchemaVersion                  | Text          | Aktuell version av schemat, till exempel **V2**            |
| Status                          | Text          | Tillståndet för objektet för säkerhetskopiering, till exempel Aktiv, Borttagen |
| VaultUniqueId                  | Text          | Unik identifierare för det skyddade valvet som är associerat med den skyddade instansen |
| SourceSystem                   | Text          | Källsystem för aktuella data - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Den här tabellen innehåller information om jobbrelaterade fält.

| **Field**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| OperationName                  | Text          | Det här fältet representerar namnet på den aktuella operationen - Jobb    |
| Kategori                       | Text          | Det här fältet representerar kategorin diagnostikdata som skjuts till Azure Monitor-loggar - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | Fält för att ange om jobbet är Ad Hoc eller Schemalagt           |
| BackupItemUniqueId             | Text          | Unikt ID som används för att identifiera säkerhetskopieringsobjektet som är relaterat till lagringsentiteten |
| BackupManagementServerUniqueId | Text          | Unikt ID som används för att identifiera hanteringsservern för säkerhetskopiering relaterad till lagringsentiteten |
| BackupManagementType           | Text          | Providertyp för säkerhetskopiering, till exempel IaaSVM, FileFolder som den här aviseringen tillhör |
| DatatransferredInMB            | Tal        | Data som överförs i MB för det här jobbet                          |
| JobbVarelseInSecs              | Tal        | Total jobblängd i sekunder                                |
| JobbFailureCode                 | Text          | Felkodsträngen på grund av vilket jobbfel inträffade    |
| Jobboperation                   | Text          | Åtgärd för vilket jobb körs, till exempel Säkerhetskopiering, Återställning, Konfigurera säkerhetskopiering |
| JobboperationSubType            | Text          | Undertyp för projektoperationen. Till exempel "Logga", när det gäller Loggsäkerhetsjobb |
| JobStartDateTime               | DateTime      | Datum och tid när jobbet började köras                       |
| JobStatus                      | Text          | Status för det färdiga jobbet, till exempel Slutförd, Misslyckades   |
| JobUniqueId                    | Text          | Unikt ID för att identifiera jobbet                                |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade servern som är associerad med aviseringen |
| ÅtervinningJobDestination         | Text          | Destination för ett återställningsjobb, där data återställs   |
| ÅterhämtningJobRPDateTime          | DateTime      | Datum, tid då återställningspunkten som återställs skapades skapades |
| ÅterhämtningJobLocation            | Text          | Platsen där återställningspunkten som återvinns lagrades |
| RecoveryLocationType           | Text          | Typ av återställningsplats                                |
| SchemaVersion                  | Text          | Aktuell version av schemat, till exempel **V2**            |
| Status                          | Text          | Aktuellt tillstånd för varningsobjektet, till exempel Aktiv, Borttagen |
| VaultUniqueId                  | Text          | Unik identifierare för det skyddade valvet som är associerat med aviseringen |
| SourceSystem                   | Text          | Källsystem för aktuella data - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Den här tabellen innehåller information om principrelaterade fält.

| **Field**                       | **Datatyp**  | **Beskrivning**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | Unik identifierare för den resurs som data samlas in om. Ett resurs-ID för Återställningstjänster |
| OperationName                   | Text           | Namnet på åtgärden, till exempel Policy eller PolicyAssociation |
| Kategori                        | Text           | Kategori av diagnostikdata som skjuts till Azure Monitor-loggar - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | Dagar i veckan när säkerhetskopieringar har schemalagts            |
| BackupFrequency                 | Text           | Frekvens med vilken säkerhetskopior körs. Till exempel, dagligen, veckovis |
| BackupManagementType            | Text           | Providertyp för servern som utför säkerhetskopieringsjobb. Till exempel IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Text           | Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt |
| Tid för säkerhetskopiering                     | Text           | Datum och tid då säkerhetskopieringar schemaläggs                     |
| DagligretentionDuration          | Heltal   | Total kvarhållningstid i dagar för konfigurerade säkerhetskopior      |
| DagligaRetentionTimes             | Text           | Datum och tid då daglig kvarhållning har konfigurerats            |
| DiffBackupDaysOfTheWeek         | Text           | Veckodagar för differentiella säkerhetskopior för SQL i Azure VM Backup |
| DiffBackupFormat                | Text           | Format för differentiella säkerhetskopior för SQL i Azure VM-säkerhetskopiering   |
| DiffBackupRetentionDuration     | Decimaltal | Kvarhållningslängd för differentiella säkerhetskopieringar för SQL i Azure VM Backup |
| DiffBackupTime                  | Tid           | Tid för differentiella säkerhetskopieringar för SQL i Azure VM Backup     |
| LogBackupFrequency              | Decimaltal | Frekvens för log-säkerhetskopior för SQL                            |
| LogBackupRetentionDuration      | Decimaltal | Kvarhållningslängd för loggsäkerhetskopior för SQL i Azure VM Backup |
| MonthlyRetentionDaysOfTheMonth  | Text           | Veckor i månaden när månatlig kvarhållning har konfigurerats.  Till exempel, Först, Sist, etc. |
| MonthlyRetentionDaysOfTheWeek   | Text           | Veckodagar som valts ut för månatlig kvarhållning              |
| MånatligaRetentionDuration        | Text           | Total kvarhållningstid i månader för konfigurerade säkerhetskopior    |
| MonthlyRetentionFormat          | Text           | Typ av konfiguration för månatlig kvarhållning. Till exempel, dagligen för dagbaserad, veckovis för veckobaserad |
| MånatligaRetentionTimes           | Text           | Datum och tid då månatlig kvarhållning har konfigurerats           |
| MonthlyRetentionWeeksOfTheMonth | Text           | Veckor i månaden när månatlig kvarhållning har konfigurerats.   Till exempel, Först, Sist, etc. |
| PolicyName                      | Text           | Namnet på den definierade principen                                   |
| PolicyUniqueId                  | Text           | Unikt ID för att identifiera principen                             |
| PolicyTimeZone                  | Text           | Tidszon där principtidsfälten anges i loggarna |
| RetentionDuration               | Text           | Kvarhållningslängd för konfigurerade säkerhetskopior                    |
| RetentionType                   | Text           | Typ av kvarhållning                                            |
| SchemaVersion                   | Text           | Det här fältet betecknar aktuell version av schemat, det är **V2** |
| Status                           | Text           | Principobjektets aktuella tillstånd. Till exempel Aktiv, Borttagen |
| SynkroniseringFrekventperday  | Heltal   | Antal gånger under en dag som en säkerhetskopiering av filer synkroniseras för SC DPM och MABS |
| VaultUniqueId                   | Text           | Unikt ID för valvet som den här principen tillhör          |
| Veckans Avgångardagarivecka    | Text           | Veckodagar som valts ut för kvarhållning varje vecka               |
| VeckorenationUnderation         | Decimaltal | Total veckokvarhållningslängd i veckor för konfigurerade säkerhetskopior |
| Veckotider            | Text           | Datum och tid då veckokvarhållning har konfigurerats            |
| ÅrligaRetionDaysOfTheMonth   | Text           | Datum för den månad som valts för årlig kvarhållning             |
| ÅrligaRetionDaysOfTheWeek    | Text           | Veckodagar som valts ut för årlig kvarhållning               |
| ÅrligtRetentionDuration         | Decimaltal | Total kvarhållningstid i år för konfigurerade säkerhetskopieringar     |
| ÅrligtRetentionFormat           | Text           | Typ av konfiguration för årlig kvarhållning, till exempel dagligen för dagbaserad, veckovis för veckobaserad |
| ÅrligaRentionMonthsOfTheYear  | Text           | Månader av året som valts för årlig retention             |
| ÅrligaRetentionTimes            | Text           | Datum och tid då årlig kvarhållning har konfigurerats            |
| ÅrligaRetentionWeeksOfTheMonth  | Text           | Veckor i månaden som valts ut för årlig kvarhållning             |
| SourceSystem                    | Text           | Källsystem för aktuella data - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Den här tabellen innehåller information om lagringsrelaterade fält.

| **Field**                      | **Datatyp** | **Beskrivning**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Resursidentifierare för data som samlas in. Till exempel Recovery Services valv resurs-ID |
| OperationName                  | Text          | Det här fältet representerar namnet på den aktuella åtgärden - Storage eller StorageAssociation |
| Kategori                       | Text          | Det här fältet representerar kategorin diagnostikdata som skjuts till Azure Monitor-loggar - AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | Unikt ID som används för att identifiera säkerhetskopieringsobjektet för virtuella datorer som säkerhetskopierats med DPM, MABS |
| BackupManagementServerUniqueId | Text          | Fält för att unikt identifiera säkerhetskopieringshanteringsservern som säkerhetskopieringsobjektet är skyddat genom, om tillämpligt |
| BackupManagementType           | Text          | Providertyp för servern som utför säkerhetskopieringsjobb. Till exempel IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Text          | Arbetsbelastning för vilken den här volymen är den önskade lagringen      |
| ProtectedContainerUniqueId     | Text          | Unik identifierare för den skyddade servern som är associerad med aviseringen |
| SchemaVersion                  | Text          | Version av schemat. **V2**                   |
| Status                          | Text          | Tillståndet för objektet för säkerhetskopieringsobjektet. Till exempel Aktiv, Borttagen |
| LagringAllokeradeinber          | Tal        | Lagringsstorlek som allokerats av motsvarande säkerhetskopieringsobjekt i motsvarande lagring av typen Disk |
| LagringKonsumentinMBs           | Tal        | Lagringsstorlek som förbrukas av motsvarande säkerhetskopieringsobjekt i motsvarande lagring |
| StorageName (Lagringsnamn)                    | Text          | Namn på lagringsenhet. Till exempel E:\                      |
| LagringTotalsizeInGBs          | Text          | Total lagringsstorlek, i GB, förbrukad av lagringsenhet     |
| StorageType (lagringstyp)                    | Text          | Typ av lagring, till exempel Moln, Volym, Disk             |
| LagringUniqueId                | Text          | Unikt ID som används för att identifiera lagringsentiteten                |
| VaultUniqueId                  | Text          | Unikt ID som används för att identifiera valvet som är relaterat till lagringsentiteten |
| VolumeFriendlyName             | Text          | Eget namn på lagringsvolymen                          |
| SourceSystem                   | Text          | Källsystem för aktuella data - Azure                    |

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du skickar diagnostikdata till Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Lär dig hur du skriver frågor i resursspecifika tabeller](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)