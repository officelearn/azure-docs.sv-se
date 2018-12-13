---
title: Datamodell för Azure Backup
description: Den här artikeln handlar om Power BI data modellinformation för Azure Backup-rapporter.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 573b7e9c5c44c7162b4020f1ef54b8986003c0b5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877141"
---
# <a name="data-model-for-azure-backup-reports"></a>Datamodell för Azure Backup-rapporter
Den här artikeln beskriver Power BI-datamodell som används för att skapa Azure Backup-rapporter. Med den här datamodellen kan du filtrera befintliga rapporter baserat på relevanta fälten och mer är dock skapa egna rapporter med hjälp av tabeller och fält i modellen. 

## <a name="creating-new-reports-in-power-bi"></a>Skapa nya rapporter i Power BI
Powerbi ger anpassade funktioner med hjälp av som du kan [skapa rapporter med datamodellen](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Med hjälp av Azure Backup-datamodell
Du kan använda följande fält som ingår i datamodellen för att skapa rapporter och anpassa befintliga rapporter.

### <a name="alert"></a>Varning
Den här tabellen innehåller grundläggande fält och aggregeringar över olika aviseringar relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Heltal |Antal aviseringar skapade i den valda tidsperioden |
| % ActiveAlertsCreatedInPeriod |Procent |Procentandelen aktiva aviseringar under vald tidsperiod |
| % CriticalAlertsCreatedInPeriod |Procent |Procentandel av kritiska aviseringar under vald tidsperiod |
| AlertOccurrenceDate |Date |Datum då aviseringen skapades |
| AlertSeverity |Text |Aviseringens exempelvis kritisk allvarlighetsgrad |
| AlertStatus |Text |Status för aviseringen till exempel aktiv |
| AlertType |Text |Typ av genererade aviseringen till exempel säkerhetskopiering |
| AlertUniqueId |Text |Unikt Id för den genererade aviseringen |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Decimaltal |Genomsnittlig tid (i minuter) för att lösa aviseringen för vald tidsperiod |
| EntityState |Text |Aktuell status för aviseringen objektet till exempel aktiv, har tagits bort |

### <a name="backup-item"></a>Säkerhetskopieringsobjekt
Den här tabellen innehåller grundläggande fält och aggregeringar över olika säkerhetskopierade objekt-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #BackupItems |Heltal |Antal säkerhetskopieringsobjekt |
| #UnprotectedBackupItems |Heltal |Antalet objekt har stoppats för skydd eller konfigurerad för säkerhetskopiering men säkerhetskopior som inte har startats|
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| BackupItemFriendlyName |Text |Eget namn på säkerhetskopieringsobjekt |
| BackupItemId |Text |ID för säkerhetskopieringsobjekt |
| BackupItemName |Text |Namnet på säkerhetskopieringsobjektet |
| BackupItemType |Text |Typ av säkerhetskopieringsobjekt exempelvis VM FileFolder |
| EntityState |Text |Aktuell status för objektet säkerhetskopieringsobjekt som till exempel aktiv, har tagits bort |
| LastBackupDateTime |Datum/tid |Tid för senaste säkerhetskopiering för valda säkerhetskopieringsobjektet |
| LastBackupState |Text |Status för senaste säkerhetskopiering för valda säkerhetskopieringsobjekt t.ex, lyckades eller misslyckades |
| LastSuccessfulBackupDateTime |Datum/tid |Tid för senaste säkerhetskopiering för valda säkerhetskopieringsobjektet |
| ProtectionState |Text |Den aktuella protection tillståndet för säkerhetskopieringsobjektet som t.ex, skyddade eller ProtectionStopped |

### <a name="calendar"></a>Kalender
Den här tabellen innehåller information om kalendern-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Date |Date |Datum som valts för att filtrera data |
| DateKey |Text |Unik nyckel för varje datum-objekt |
| DayDiff |Decimaltal |Skillnad i dag för att filtrera data, till exempel, 0 indikerar aktuell dag data, -1 anger föregående en dags data, 0 och -1 anger data för aktuella och föregående dag  |
| Månad |Text |Månad på året som valts för att filtrera data, månad börjar på första dag och slutar gälla 31: a dag |
| MonthDate | Date |Datum i månaden då månad slutar valts för att filtrera data |
| MonthDiff |Decimaltal |Skillnad i månad för data som filtrerande till exempel, 0 indikerar aktuella månadens data, -1 anger föregående månad data, 0 och -1 anger data för aktuella och föregående månad |
| Vecka |Text |Veckan har valts för att filtrera data, veckan som börjar med söndag och slutar på lördag |
| WeekDate |Date |Datum i veckan då vecka slutar valts för att filtrera data |
| WeekDiff |Decimaltal |Skillnad i veckan, filtrera data, till exempel, 0 indikerar aktuella veckan data, -1 anger data i föregående vecka, 0 och -1 anger data för aktuella och föregående vecka |
| År |Text |Kalenderår som valts för att filtrera data |
| YearDate |Date |Datum under året när året slutar valts för att filtrera data |

### <a name="job"></a>Jobb
Den här tabellen innehåller grundläggande fält och aggregeringar över olika jobbrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #JobsCreatedInPeriod |Heltal |Antalet jobb som skapats i den valda tidsperioden |
| % FailuresForJobsCreatedInPeriod |Procent |Procentandel total köras i den valda tidsperioden |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Decimaltal |80 percentilvärdet för data som överförs i MB för **säkerhetskopiering** jobb som skapats i den valda tidsperioden |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Decimaltal |Genomsnittlig tid i minuter för **slutförda backup** jobb som skapats i den valda tidsperioden |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Decimaltal |Genomsnittlig tid i minuter för **slutförts återställning** jobb som skapats i den valda tidsperioden |
| BackupStorageDestination |Text |Mål för lagring av säkerhetskopior till exempel molnet, Disk  |
| EntityState |Text |Aktuell status för objektet till exempel aktiv, har tagits bort |
| JobFailureCode |Text |Felkod sträng på grund av som hände jobbfel |
| JobOperation |Text |Åtgärden som jobbet körs exempelvis säkerhetskopiering, återställning, konfigurera säkerhetskopiering |
| JobStartDate |Date |Datum när jobbet har startats |
| JobStartTime |Tid |När du jobb började köras |
| JobStatus |Text |Status för det färdiga t.ex, slutförd eller misslyckades |
| JobUniqueId |Text |Unikt Id för att identifiera jobbet |

### <a name="policy"></a>Princip
Den här tabellen innehåller grundläggande fält och aggregeringar över olika princip-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Policies |Heltal |Antal principer för säkerhetskopiering som finns i systemet |
| #PoliciesInUse |Heltal |Antal principer som för närvarande används för att konfigurera säkerhetskopiering |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| BackupDaysOfTheWeek |Text |Dagar i veckan när säkerhetskopieringar har schemalagts |
| BackupFrequency |Text |Frekvens med vilken säkerhetskopieringar körs till exempel, varje dag, varje vecka |
| BackupTimes |Text |Datum och tid när säkerhetskopieringar är schemalagda |
| DailyRetentionDuration |Heltal |Totalt antal kvarhållning varaktighet i dagar för konfigurerade säkerhetskopieringar |
| DailyRetentionTimes |Text |Datum och tid när bevarande varje dag har konfigurerats |
| EntityState |Text |Aktuell status för principobjektet till exempel aktiv, har tagits bort |
| MonthlyRetentionDaysOfTheMonth |Text |Datum i månaden som valts för kvarhållning av månatlig |
| MonthlyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för kvarhållning av månatlig |
| MonthlyRetentionDuration |Decimaltal |Totalt antal kvarhållningsvaraktighetens i månader för konfigurerade säkerhetskopieringar |
| MonthlyRetentionFormat |Text |Typ av konfigurationen för kvarhållning av månatlig till exempel varje dag för dag-baserat, varje vecka för vecka baserat |
| MonthlyRetentionTimes |Text |Datum och tid när månatliga kvarhållning har konfigurerats |
| MonthlyRetentionWeeksOfTheMonth |Text |Veckor i månaden när kvarhållning av månatlig är konfigurerats exempelvis första, sista osv. |
| Principnamn |Text |Namnet på den princip som har definierats |
| PolicyUniqueId |Text |Unikt Id för att identifiera principen |
| RetentionType |Text |Typ av bevarandeprincipen till exempel, varje dag, vecka, månad, varje år |
| WeeklyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för kvarhållning av veckovis |
| WeeklyRetentionDuration |Decimaltal |Total varaktighet för varje vecka kvarhållning i veckor för konfigurerade säkerhetskopieringar |
| WeeklyRetentionTimes |Text |Datum och tid när kvarhållning av veckovis har konfigurerats |
| YearlyRetentionDaysOfTheMonth |Text |Datum i månaden som valts för kvarhållning av årlig |
| YearlyRetentionDaysOfTheWeek |Text |Dagar i veckan har valts för kvarhållning av årlig |
| YearlyRetentionDuration |Decimaltal |Totalt antal kvarhållningsvaraktighetens i år för konfigurerade säkerhetskopior |
| YearlyRetentionFormat |Text |Typ av konfigurationen för kvarhållning av årlig till exempel varje dag för dag-baserat, varje vecka för vecka baserat |
| YearlyRetentionMonthsOfTheYear |Text |Månader som har valts för kvarhållning av årlig |
| YearlyRetentionTimes |Text |Datum och tid när årliga kvarhållning har konfigurerats |
| YearlyRetentionWeeksOfTheMonth |Text |Veckor i månaden när kvarhållning av årlig är konfigurerats exempelvis första, sista osv. |

### <a name="protected-server"></a>Skyddad Server
Den här tabellen innehåller grundläggande fält och aggregeringar över olika skyddade server-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedServers |Heltal |Antal skyddade servrar |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AzureBackupAgentOSType |Text |OS-typ av Azure Backup-agenten |
| AzureBackupAgentOSVersion |Text |OS-versionen av Azure Backup-agenten |
| AzureBackupAgentUpdateDate |Text |Datum när agenten Backup-agenten uppdaterades |
| AzureBackupAgentVersion |Text |Versionsnumret för Backup-Agentversion |
| BackupManagementType |Text |Typ av provider för att utföra säkerhetskopieringen exempelvis IaaSVM FileFolder |
| EntityState |Text |Aktuell status för skyddade server-objekt till exempel aktiv, har tagits bort |
| ProtectedServerFriendlyName |Text |Eget namn på skyddad server |
| ProtectedServerName |Text |Namnet på skyddad server |
| ProtectedServerType |Text |Typ av skyddad server säkerhetskopieras till exempel IaaSVMContainer |
| ProtectedServerName |Text |Namnet på skyddad server till vilken säkerhetskopieringsobjekt tillhör |
| RegisteredContainerId |Text |ID för behållare som har registrerats för säkerhetskopiering |

### <a name="storage"></a>Storage
Den här tabellen innehåller grundläggande fält och aggregeringar över olika storage-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #ProtectedInstances |Decimaltal |Antalet skyddade instanser som används för att beräkna frontend-lagring i fakturering, beräknad baserat på senaste värdet i vald tid |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| CloudStorageInMB |Decimaltal |Säkerhetskopiering molnlagring som används av säkerhetskopieringar, beräknas baserat på senaste värdet i vald tid |
| EntityState |Text |Aktuell status för objektet till exempel aktiv, har tagits bort |
| LastUpdatedDate |Date |Datum då vald rad senast uppdaterades |

### <a name="time"></a>Tid
Den här tabellen innehåller information om tidsrelaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| Timme |Tid |Timme på dagen till exempel 1:00:00 PM |
| HourNumber |Decimaltal |Timme numret i dag exempelvis 13,00 |
| Minut |Decimaltal |Minuten i timmen |
| PeriodOfTheDay |Text |Period tidslucka i dag exempelvis 12-3 AM |
| Tid |Tid |Den tid på dagen till exempel 12:00:01: 00 |
| TimeKey |Text |Nyckelvärdet för att representera tid |

### <a name="vault"></a>Valv
Den här tabellen innehåller grundläggande fält och aggregeringar över olika vault-relaterade fält.

| Fält | Datatyp | Beskrivning |
| --- | --- | --- |
| #Vaults |Heltal |Antalet valv |
| AsOnDateTime |Datum/tid |Senaste Uppdateringstid för den markerade raden |
| AzureDataCenter |Text |Datacenter där valvet finns |
| EntityState |Text |Aktuell status för valvobjekt till exempel aktiv, har tagits bort |
| StorageReplicationType |Text |Typ av lagringsreplikering för valvet exempelvis GeoRedundant |
| SubscriptionId |Text |Prenumerations-Id för kunden som har valts för att generera rapporter |
| VaultName |Text |Namnet på valvet |
| VaultTags |Text |Taggar som är kopplade till valvet |

## <a name="next-steps"></a>Nästa steg
När du har läst datamodellen för att skapa Azure Backup-rapporter, finns i följande artiklar för mer information om att skapa och visa rapporter i Power BI.

* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
