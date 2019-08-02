---
title: Data modell för Azure Backup
description: Den här artikeln beskriver information om Power BI data modell för Azure Backup rapporter.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: 53051c88d5723f61fa08e431bdf7ab531e88123e
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689153"
---
# <a name="data-model-for-azure-backup-reports"></a>Datamodell för Azure Backup-rapporter
I den här artikeln beskrivs Power BI data modell som används för att skapa Azure Backup rapporter. Med den här data modellen kan du filtrera befintliga rapporter baserat på relevanta fält och på ett mer viktigt, skapa egna rapporter med hjälp av tabeller och fält i modellen. 

## <a name="creating-new-reports-in-power-bi"></a>Skapa nya rapporter i Power BI
Power BI innehåller anpassnings funktioner som du kan använda [för att skapa rapporter med hjälp av data modellen](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Använda Azure Backup data modell
Du kan använda följande fält som ingår i data modellen för att skapa rapporter och anpassa befintliga rapporter.

### <a name="alert"></a>Varning
Den här tabellen innehåller grundläggande fält och agg regeringar för olika fält för aviseringar.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Heltal |Antal aviseringar som skapats under den valda tids perioden |
| %ActiveAlertsCreatedInPeriod |Procent |Procent andel aktiva aviseringar under den valda tids perioden |
| %CriticalAlertsCreatedInPeriod |Procent |Procent av kritiska aviseringar under den valda tids perioden |
| AlertOccurrenceDate |Date |Datum när avisering skapades |
| AlertSeverity |Text |Allvarlighets grad för aviseringen, till exempel kritisk |
| Alert status |Text |Status för aviseringen, till exempel aktiv |
| AlertType |Text |Typ av genererad avisering till exempel säkerhets kopiering |
| AlertUniqueId |Text |Unikt ID för den genererade aviseringen |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Decimal tal |Genomsnittlig tid (i minuter) för att lösa aviseringar för den valda tids perioden |
| EntityState |Text |Aktuellt tillstånd för aviserings objekt, t. ex. aktiv, borttagen |

### <a name="backup-item"></a>Säkerhets kopierings objekt
Den här tabellen innehåller grundläggande fält och agg regeringar för olika fält för säkerhetskopierade objekt.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #BackupItems |Heltal |Antal säkerhets kopierings objekt |
| #UnprotectedBackupItems |Heltal |Antal säkerhets kopierings objekt som har stoppats för skydd eller som inte har kon figurer ATS för säkerhets kopiering|
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| BackupItemFriendlyName |Text |Eget namn på säkerhets kopierings objekt |
| BackupItemId |Text |ID för säkerhets kopierings objekt |
| BackupItemName |Text |Namn på säkerhets kopierings objekt |
| BackupItemType |Text |Typ av säkerhets kopierings objekt, till exempel virtuell dator, FileFolder |
| EntityState |Text |Aktuellt tillstånd för objektet för säkerhets kopiering, till exempel aktivt, borttaget |
| LastBackupDateTime |Date/Time |Tid för senaste säkerhets kopiering för valt säkerhets kopierings objekt |
| LastBackupState |Text |Tillstånd för den senaste säkerhets kopieringen för det valda säkerhets kopierings objektet, till exempel lyckad, misslyckades |
| LastSuccessfulBackupDateTime |Date/Time |Tid för senaste lyckade säkerhets kopiering för valt säkerhets kopierings objekt |
| ProtectionState |Text |Aktuellt skydds tillstånd för säkerhets kopierings objekt, till exempel, skyddat, ProtectionStopped |

### <a name="calendar"></a>Kalender
Den här tabellen innehåller information om kalender relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Date |Date |Datum som valts för filtrering av data |
| DateKey |Text |Unik nyckel för varje datum objekt |
| DayDiff |Decimal tal |Skillnad på dag för filtrering av data, till exempel 0 anger dagens data,-1 anger föregående data för en dag, 0 och-1 visar data för aktuell och föregående dag  |
| Månad |Text |Månad på det år som valts för filtrering av data, månad börjar den första dagen och slutar på 31: a dag |
| MonthDate | Date |Datum i månaden då månaden slutar, valt för filtrering av data |
| MonthDiff |Decimal tal |Skillnad i månad för filtrering av data, till exempel 0 anger aktuell månads data,-1 anger föregående månads data, 0 och-1 visar data för aktuell och föregående månad |
| Vecka |Text |Vecka som valts för att filtrera data, veckan börjar på söndag och slutar på lördag |
| WeekDate |Date |Datum i veckan då veckan slutar, valt för filtrering av data |
| WeekDiff |Decimal tal |Skillnaden i veckan för att filtrera data, till exempel 0 anger den aktuella veckans data,-1 anger föregående veckas data, 0 och-1 visar data för aktuell och föregående vecka |
| År |Text |Kalender år valt för filtrering av data |
| YearDate |Date |Datum under året då året slutar, valt för filtrering av data |

### <a name="job"></a>Jobb
Den här tabellen innehåller grundläggande fält och agg regeringar för olika projektrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #JobsCreatedInPeriod |Heltal |Antal jobb som har skapats under den valda tids perioden |
| %FailuresForJobsCreatedInPeriod |Procent |Procent andel övergripande jobb haverier under den valda tids perioden |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Decimal tal |80th percentil värde av data som överförts i MB för **säkerhets kopierings** jobb som skapats under den valda tids perioden |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Decimal tal |Genomsnittlig tid i minuter för **slutförda säkerhets kopierings** jobb som skapats under vald tids period |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Decimal tal |Genomsnittlig tid i minuter för **slutförda** återställnings jobb som skapats under den valda tids perioden |
| BackupStorageDestination |Text |Målet för lagring av säkerhets kopior, till exempel Cloud, disk  |
| EntityState |Text |Aktuellt tillstånd för jobbobjektet, t. ex. aktiv, borttagen |
| JobFailureCode |Text |Kod sträng för felkod på grund av vilket jobbfel som skett |
| JobOperation |Text |Åtgärd för vilket jobb som körs, till exempel säkerhets kopiering, återställning, konfigurera säkerhets kopiering |
| JobStartDate |Date |Datum när jobbet började köras |
| JobStartTime |Time |Tid när jobbet började köras |
| JobStatus |Text |Status för det slutförda jobbet, till exempel slutfört, misslyckades |
| JobUniqueId |Text |Unikt ID för att identifiera jobbet |

### <a name="policy"></a>Princip
Den här tabellen innehåller grundläggande fält och agg regeringar för olika principbaserade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Policies |Heltal |Antal säkerhets kopierings principer som finns i systemet |
| #PoliciesInUse |Heltal |Antal principer som för närvarande används för att konfigurera säkerhets kopior |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| BackupDaysOfTheWeek |Text |Vecko dagar när säkerhets kopieringar har schemalagts |
| BackupFrequency |Text |Den frekvens med vilken säkerhets kopieringar körs, till exempel varje dag, varje vecka |
| BackupTimes |Text |Datum och tid när säkerhets kopieringar schemaläggs |
| DailyRetentionDuration |Heltal |Total Retentions tid i dagar för konfigurerade säkerhets kopieringar |
| DailyRetentionTimes |Text |Datum och tid när daglig kvarhållning hade kon figurer ATS |
| EntityState |Text |Aktuellt tillstånd för principobjektet, t. ex. aktiv, borttagen |
| MonthlyRetentionDaysOfTheMonth |Text |Datum i månaden som valts för månatlig kvarhållning |
| MonthlyRetentionDaysOfTheWeek |Text |Vecko dagar som valts för månatlig kvarhållning |
| MonthlyRetentionDuration |Decimal tal |Total Retentions tid i månader för konfigurerade säkerhets kopieringar |
| MonthlyRetentionFormat |Text |Typ av konfiguration för månatlig kvarhållning, till exempel dagligen för dag baserat, veckovis för vecka, baserat |
| MonthlyRetentionTimes |Text |Datum och tid när månatlig kvarhållning har kon figurer ATS |
| MonthlyRetentionWeeksOfTheMonth |Text |Veckor i månaden då månatlig kvarhållning har kon figurer ATS till exempel första, sist osv. |
| PolicyName |Text |Namn på den princip som definierats |
| PolicyUniqueId |Text |Unikt ID för att identifiera principen |
| RetentionType |Text |Typ av bevarande princip, till exempel varje dag, varje vecka, varje månad, varje år |
| WeeklyRetentionDaysOfTheWeek |Text |Vecko dagar som valts för veckovis kvarhållning |
| WeeklyRetentionDuration |Decimal tal |Total varaktighet för veckovis kvarhållning i veckor för konfigurerade säkerhets kopieringar |
| WeeklyRetentionTimes |Text |Datum och tid när veckovis kvarhållning har kon figurer ATS |
| YearlyRetentionDaysOfTheMonth |Text |Datum för den månad som valts för årlig kvarhållning |
| YearlyRetentionDaysOfTheWeek |Text |Vecko dagar som valts för årlig kvarhållning |
| YearlyRetentionDuration |Decimal tal |Total Retentions tid i år för konfigurerade säkerhets kopieringar |
| YearlyRetentionFormat |Text |Typ av konfiguration för årlig kvarhållning, till exempel dagligen för dag baserat, veckovis för vecka, baserat |
| YearlyRetentionMonthsOfTheYear |Text |Månader under året som valts för årlig kvarhållning |
| YearlyRetentionTimes |Text |Datum och tid när årlig kvarhållning har kon figurer ATS |
| YearlyRetentionWeeksOfTheMonth |Text |Veckor i månaden när årlig kvarhållning har kon figurer ATS till exempel första, sist osv. |

### <a name="protected-server"></a>Skyddad Server
Den här tabellen innehåller grundläggande fält och agg regeringar för olika skyddade Server-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedServers |Heltal |Antal skyddade servrar |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| AzureBackupAgentOSType |Text |OS-typ för Azure Backup Agent |
| AzureBackupAgentOSVersion |Text |OS-version av Azure Backup Agent |
| AzureBackupAgentUpdateDate |Text |Datum när agenten för säkerhets kopiering uppdaterades |
| AzureBackupAgentVersion |Text |Versions nummer för agentens säkerhets kopierings version |
| BackupManagementType |Text |Providertyp för att utföra säkerhets kopiering till exempel, IaaSVM, FileFolder |
| EntityState |Text |Aktuellt tillstånd för det skyddade Server objektet, till exempel aktivt, borttaget |
| ProtectedServerFriendlyName |Text |Eget namn på skyddad Server |
| ProtectedServerName |Text |Namn på skyddad Server |
| ProtectedServerType |Text |Typ av skyddad server som har säkerhetskopierats, till exempel IaaSVMContainer |
| ProtectedServerName |Text |Namnet på den skyddade server som säkerhetskopierade objekt tillhör |
| RegisteredContainerId |Text |ID för den behållare som registrerats för säkerhets kopiering |

### <a name="storage"></a>Storage
Den här tabellen innehåller grundläggande fält och agg regeringar för olika lagrings fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedInstances |Decimal tal |Antal skyddade instanser som används för att beräkna frontend-lagring vid fakturering, beräknat baserat på det senaste värdet i vald tid |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| CloudStorageInMB |Decimal tal |Lagring av moln säkerhets kopior som används av säkerhets kopieringar, beräknat baserat på det senaste värdet i vald tid |
| EntityState |Text |Objektets aktuella tillstånd, t. ex. aktiv, borttagen |
| LastUpdatedDate |Date |Datum när den markerade raden senast uppdaterades |

### <a name="time"></a>Time
Den här tabellen innehåller information om tidsrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Timme |Time |Timme på dagen till exempel 1:00:00 PM |
| HourNumber |Decimal tal |Timmes nummer på dagen till exempel 13,00 |
| Minut |Decimal tal |Minut i timmen |
| PeriodOfTheDay |Text |Tids period under dagen till exempel 12-3 AM |
| Time |Time |Tid på dagen till exempel 12:00:01 AM |
| TimeKey |Text |Nyckel värde som representerar tid |

### <a name="vault"></a>Valv
Den här tabellen innehåller grundläggande fält och agg regeringar för olika valv-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Vaults |Heltal |Antal valv |
| AsOnDateTime |Date/Time |Senaste uppdaterings tid för den valda raden |
| AzureDataCenter |Text |Data Center där valvet finns |
| EntityState |Text |Aktuellt tillstånd för Valve-objektet till exempel aktivt, borttaget |
| StorageReplicationType |Text |Typ av lagrings replikering för valvet, t. ex., förredundant |
| SubscriptionId |Text |Prenumerations-ID för den valda kunden för generering av rapporter |
| VaultName |Text |Namn på valvet |
| VaultTags |Text |Taggar som är kopplade till valvet |

## <a name="next-steps"></a>Nästa steg
När du har granskat data modellen för att skapa Azure Backup rapporter kan du läsa följande artiklar för mer information om hur du skapar och visar rapporter i Power BI.

* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
