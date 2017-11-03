---
title: "Datamodellen för Azure Backup"
description: "Den här artikeln handlar om Power BI data modellinformation för Azure Backup-rapporter."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 0767c330-690d-474d-85a6-aa8ddc410bb2
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: efecbc9f1c410744f49795889c4ec3cc618f07e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-model-for-azure-backup-reports"></a>Datamodell för Azure Backup-rapporter
Den här artikeln beskriver Power BI-datamodell som används för att skapa rapporter för Azure Backup. Med den här datamodellen, kan du filtrera befintliga rapporter baserat på relevanta fält och mer av allt skapa egna rapporter med hjälp av tabeller och fält i modellen. 

## <a name="creating-new-reports-in-power-bi"></a>Skapa nya rapporter i Power BI
Powerbi ger anpassningsfunktionerna med hjälp av som du kan [skapa rapporter med datamodellen](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Med hjälp av Azure Backup-datamodell
Du kan använda följande fält som ingår i datamodellen för att skapa rapporter och anpassa befintliga rapporter.

### <a name="alert"></a>Varning
Den här tabellen innehåller grundläggande fält och aggregeringar över olika aviseringar relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Heltal |Antalet aviseringar som skapats i den valda tidsperioden |
| % ActiveAlertsCreatedInPeriod |Procent |Procentandelen aktiva aviseringar under vald tidsperiod |
| % CriticalAlertsCreatedInPeriod |Procent |Andelen kritiska aviseringar tidsperioden |
| AlertOccurenceDate |Date |Datum då aviseringen skapades |
| AlertSeverity |Text |Allvarlighetsgrad för aviseringen exempelvis kritisk |
| AlertStatus |Text |Status för aviseringen exempelvis aktiv |
| AlertType |Text |Typ av genererade aviseringen exempelvis säkerhetskopiering |
| AlertUniqueId |Text |Unikt Id för den genererade aviseringen |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Decimaltal |Genomsnittlig tid (i minuter) för att lösa aviseringen för vald tidsperiod |
| EntityState |Text |Aktuell status för objektet avisering exempelvis aktiv, tagits bort |

### <a name="backup-item"></a>Säkerhetskopiera objekt
Den här tabellen innehåller grundläggande fält och aggregeringar över olika säkerhetskopiering objektet-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #BackupItems |Heltal |Antal objekt som säkerhetskopiering |
| #UnprotectedBackupItems |Heltal |Antal Säkerhetskopiera objekt har stoppats för skydd eller har konfigurerats för säkerhetskopiering men säkerhetskopieringar som inte har startats|
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| BackupItemFriendlyName |Text |Eget namn för Säkerhetskopiera objekt |
| BackupItemId |Text |Säkerhetskopiera objekt-ID |
| BackupItemName |Text |Namnet på Säkerhetskopiera objekt |
| BackupItemType |Text |Typ av säkerhetskopiering objekt till exempel Virtuella FileFolder |
| EntityState |Text |Aktuell status för objektet säkerhetskopieobjekt exempelvis aktiv, tagits bort |
| LastBackupDateTime |Datum/tid |Tid för senaste säkerhetskopiering för det valda objektet för säkerhetskopiering |
| LastBackupState |Text |Tillståndet för den senaste säkerhetskopieringen för det valda säkerhetskopiering objektet exempelvis lyckades, misslyckades |
| LastSuccessfulBackupDateTime |Datum/tid |Tid för senaste lyckade säkerhetskopiering för det valda objektet för säkerhetskopiering |
| ProtectionState |Text |Säkerhetskopiera objekt till exempel skyddade, ProtectionStopped aktuella skydd tillstånd |

### <a name="calendar"></a>Kalender
Den här tabellen innehåller information om kalender-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Date |Date |Datum som valts för att filtrera data |
| DateKey |Text |Unik nyckel för varje datum-objekt |
| DayDiff |Decimaltal |Skillnad i dag för att filtrera data, till exempel, 0 anger data aktuell dag, -1 anger föregående en dags data, 0 och -1 anger data för aktuella och föregående dag  |
| Månad |Text |Månaden på året som valts för att filtrera data månad börjar på första dag och slutar på 31 dagar |
| MonthDate | Date |Datum i månaden när månad avslutas valts för att filtrera data |
| MonthDiff |Decimaltal |Skillnad i månad för att filtrera data, till exempel, 0 anger aktuella månadens uppgifter, -1 anger föregående månad data, 0 och -1 anger data för aktuella och föregående månad |
| Vecka |Text |Veckan har valts för att filtrera data, veckan börjar med söndag och slutar på lördag |
| WeekDate |Date |Dag i veckan då veckan slutar valts för att filtrera data |
| WeekDiff |Decimaltal |Skillnad i veckan för filtrera data, till exempel, 0 anger aktuella veckan data, -1 anger data för föregående vecka, 0 och -1 anger data för aktuella och föregående vecka |
| År |Text |Kalenderåret som valts för att filtrera data |
| YearDate |Date |Datum under året när året slutar valts för att filtrera data |

### <a name="job"></a>Jobb
Den här tabellen innehåller grundläggande fält och aggregeringar över olika projektspecifika fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #JobsCreatedInPeriod |Heltal |Antalet jobb som skapats i den valda tidsperioden |
| % FailuresForJobsCreatedInPeriod |Procent |Procentandel total jobbet fel i den valda tidsperioden |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Decimaltal |80 percentilvärdet för data som överförs i MB för **säkerhetskopiering** jobb som skapats i den valda tidsperioden |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Decimaltal |Genomsnittlig tid i minuter för **slutförda säkerhetskopiering** jobb som skapats i den valda tidsperioden |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Decimaltal |Genomsnittlig tid i minuter för **slutförd återställning** jobb som skapats i den valda tidsperioden |
| BackupStorageDestination |Text |Mål för säkerhetskopieringslagring exempelvis molnet Disk  |
| EntityState |Text |Aktuell status för jobbobjektet exempelvis aktiv, tagits bort |
| JobFailureCode |Text |Felkod sträng på grund av som skett jobbet misslyckades |
| JobOperation |Text |Åtgärden som jobbet körs exempelvis säkerhetskopiering, återställning, konfigurera säkerhetskopiering |
| JobStartDate |Date |Datum när jobbet har startats |
| JobStartTime |Tid |När jobbet har startats |
| JobStatus |Text |Status för jobbet klar exempelvis slutfört, misslyckades |
| JobUniqueId |Text |Unikt Id för att identifiera jobbet |

### <a name="policy"></a>Princip
Den här tabellen innehåller grundläggande fält och aggregeringar över olika principrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Policies |Heltal |Antal principer för säkerhetskopiering som finns i systemet |
| #PoliciesInUse |Heltal |Antal principer som för närvarande används för att konfigurera säkerhetskopiering |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| BackupDaysOfTheWeek |Text |Dagar i veckan när schemalagda säkerhetskopieringar |
| BackupFrequency |Text |Frekvens som säkerhetskopieringar körs exempelvis, varje dag, varje vecka |
| BackupTimes |Text |Datum och tid när säkerhetskopieringar är schemalagda |
| DailyRetentionDuration |Heltal |Totalt bevarande varaktighet i dagar för konfigurerade säkerhetskopieringar |
| DailyRetentionTimes |Text |Datum och tid när dagliga kvarhållning konfigurerades |
| EntityState |Text |Aktuell status för principobjektet exempelvis aktiv, tagits bort |
| MonthlyRetentionDaysOfTheMonth |Text |Datum i månaden som valts för månatliga kvarhållning |
| MonthlyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för månatliga kvarhållning |
| MonthlyRetentionDuration |Decimaltal |Totalt bevarande varaktighet i månader för konfigurerade säkerhetskopieringar |
| MonthlyRetentionFormat |Text |Typ av konfigurationen för kvarhållning av månatlig exempelvis varje dag för dag, varje vecka baserat på vecka baserat |
| MonthlyRetentionTimes |Text |Datum och tid när månatliga kvarhållning har konfigurerats |
| MonthlyRetentionWeeksOfTheMonth |Text |Veckor i månaden när månatliga kvarhållning är konfigurerad till exempel första, sista osv. |
| Principnamn |Text |Namnet på den princip som har definierats |
| PolicyUniqueId |Text |Unikt Id för att identifiera principen |
| RetentionType |Text |Typ av bevarandeprincip till exempel, varje dag, vecka, månad, varje år |
| WeeklyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för kvarhållning av veckovis |
| WeeklyRetentionDuration |Decimaltal |Total varaktighet för varje vecka kvarhållning i veckor för konfigurerade säkerhetskopieringar |
| WeeklyRetentionTimes |Text |Datum och tid när varje vecka kvarhållning har konfigurerats |
| YearlyRetentionDaysOfTheMonth |Text |Datum i månaden som valts för kvarhållning av årlig |
| YearlyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för kvarhållning av årlig |
| YearlyRetentionDuration |Decimaltal |Totalt bevarande varaktighet i år för konfigurerade säkerhetskopieringar |
| YearlyRetentionFormat |Text |Typ av konfigurationen för kvarhållning av årlig exempelvis varje dag för dag, varje vecka baserat på vecka baserat |
| YearlyRetentionMonthsOfTheYear |Text |Månader som har valts för kvarhållning av årlig |
| YearlyRetentionTimes |Text |Datum och tid när årlig kvarhållning har konfigurerats |
| YearlyRetentionWeeksOfTheMonth |Text |Veckor i månaden när årlig kvarhållning är konfigurerad till exempel första, sista osv. |

### <a name="protected-server"></a>Skyddad Server
Den här tabellen innehåller grundläggande fält och aggregeringar över olika skyddade server-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedServers |Heltal |Antal skyddade servrar |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AzureBackupAgentOSType |Text |OS-typen för Azure Backup-agenten |
| AzureBackupAgentOSVersion |Text |OS-versionen av Azure Backup-agenten |
| AzureBackupAgentUpdateDate |Text |Datum när agenten Backup-agenten uppdaterades |
| AzureBackupAgentVersion |Text |Versionsnumret för agenten säkerhetskopiering Version |
| BackupManagementType |Text |Providertyp för att utföra säkerhetskopieringen exempelvis IaaSVM FileFolder |
| EntityState |Text |Aktuell status för skyddade serverobjektet exempelvis aktiv, tagits bort |
| ProtectedServerFriendlyName |Text |Eget namn på skyddad server |
| ProtectedServerName |Text |Namnet på skyddad server |
| ProtectedServerType |Text |Typ av skyddade server säkerhetskopieras till exempel IaaSVMContainer |
| ProtectedServerName |Text |Namnet på skyddad server till vilken säkerhetskopiering objektet tillhör |
| RegisteredContainerId |Text |ID: t för behållaren som registrerats för säkerhetskopiering |

### <a name="storage"></a>Lagring
Den här tabellen innehåller grundläggande fält och aggregeringar över olika lagringsrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedInstances |Decimaltal |Antalet skyddade instanser som används för att beräkna klientdel lagring i fakturering, beräknad baserat på senaste värdet i vald tid |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| CloudStorageInMB |Decimaltal |Säkerhetskopiering molnlagring som används av säkerhetskopior, beräknas baserat på senaste värdet i vald tid |
| EntityState |Text |Aktuell status för objektet exempelvis aktiv, tagits bort |
| LastUpdatedDate |Date |Datum då markerade raden senast uppdaterades |

### <a name="time"></a>Tid
Den här tabellen innehåller information om tidsrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Timme |Tid |Timme på dagen till exempel 1:00:00 PM |
| HourNumber |Decimaltal |Timme nummer i dag exempelvis 13,00 |
| Minut |Decimaltal |Minuten i timmen |
| PeriodOfTheDay |Text |Period tid på dygnet exempelvis 12 3 AM |
| Tid |Tid |Den tid på dagen exempelvis 12:00:01: 00 |
| TimeKey |Text |Nyckelvärdet för att representera tid |

### <a name="vault"></a>Valv
Den här tabellen innehåller grundläggande fält och aggregeringar över olika valvet-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Vaults |Heltal |Antal valv |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AzureDataCenter |Text |Datacenter där valvet finns |
| EntityState |Text |Aktuell status för objektet valvet exempelvis aktiv, tagits bort |
| StorageReplicationType |Text |Typ av lagringsreplikering för valvet exempelvis GeoRedundant |
| SubscriptionId |Text |Prenumerations-Id för kunden som har valts för att generera rapporter |
| VaultName |Text |Namnet på valvet |
| VaultTags |Text |Taggar som är kopplat till valvet |

## <a name="next-steps"></a>Nästa steg
När du granskar datamodellen för att skapa Azure Backup-rapporter finns i följande artiklar för mer information om att skapa och visa rapporter i Power BI.

* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
