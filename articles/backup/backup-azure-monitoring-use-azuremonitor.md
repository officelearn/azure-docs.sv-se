---
title: 'Azure Backup: Övervaka Azure Backup med Azure Monitor'
description: Övervaka Azure Backup-arbetsbelastningar och skapa anpassade aviseringar med Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; Aviseringar; Diagnostikinställningar; Åtgärdsgrupper
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 7daf76605d40495d6c9cb5076f0403a78819adfc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439171"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Övervakning i stor skala med Azure Monitor

Den [inbyggd övervakning och avisering artikeln](backup-azure-monitoring-built-in-monitor.md) visas för övervakning och aviseringsfunktioner i ett enda Recovery services-valv och som erbjuds utan någon infrastruktur med ytterligare hantering. Inbyggd tjänst är dock begränsat i följande scenarier.

- Övervakning av data från flera RS valv mellan prenumerationer
- Om e-post inte är den önskade meddelandekanalen
- Om användarna vill att aviseringar ska visas för fler scenarier
- Om du vill visa information från den lokala komponent, till exempel System Center DPM (SC-DPM) i Azure, vilket inte visas i [säkerhetskopieringsjobb](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) eller [säkerhetskopiering aviseringar](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) i portalen.

## <a name="using-log-analytics-workspace"></a>Med hjälp av Log Analytics-arbetsyta

> [!NOTE]
> Data från Virtuella Azure-säkerhetskopieringar, MAB-agenten, System Center DPM (SC-DPM) är som läggs in till arbetsytan Log Analytics via diagnostikinställningar. Stöd för SQL-säkerhetskopior i Azure virtuella datorer, säkerhetskopior av Azure-filresurser, Microsoft Azure Backup Server (MABS) kommer snart.

Vi använder sig av funktionerna i två Azure-tjänster – **diagnostikinställningar** (för att skicka data från flera Azure Resource Manager-resurser till en annan resurs) och **Log Analytics** (LA - för att generera anpassade aviseringar där du kan definiera andra meddelandekanaler med åtgärdsgrupper) för att övervaka i skala. Följande avsnitt visar om hur du använder LA för att övervaka Azure Backup i stor skala.

### <a name="configuring-diagnostic-settings"></a>Konfigurera diagnostikinställningar

En Azure Resource Manager-resurs, till exempel Azure Recovery services-valvet innehåller alla möjliga information om schemalagda åtgärder och utlöses användaråtgärder som diagnostikdata. Klicka på ”diagnostikinställningarna” i avsnittet övervakning och ange målet för diagnostikdata i RS-valvet.

![RS Vault diagnostikinställning med LA som mål](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Du kan välja en LA-arbetsyta från en annan prenumeration som mål. *Du kan övervaka valv mellan prenumerationer i en enda plats genom att välja samma LA arbetsyta för flera RS-valv.* Välj ”AzureBackupReport” som loggen på channel alla Azure Backup relaterad information till arbetsytan LA.

> [!IMPORTANT]
> När du har slutfört konfigurationen, bör du vänta 24 timmar innan första data-push att slutföra. Därefter alla händelser skickas som anges i den [frekvens avsnittet](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Distribuera lösningen till Log Analytics-arbetsyta

När data finns i LA arbetsytan [distribuera en mall för github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) till LA att visualisera data. Kontrollera att du ger samma resursgrupp, namn på arbetsyta och arbetsytan, position för att korrekt identifiera arbetsytan och sedan installera den här mallen på den.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Visa Azure Backup-data med hjälp av Log Analytics (LA)

När mallen distribueras visas lösningen för övervakning av Azure Backup i den sammanfattande arbetsyteregion. Du kan bläddra i via

- Azure Monitor -> ”information” under ”Insights”-avsnittet och väljer du relevant arbetsyta eller
- Log Analytics-arbetsytor -> väljer du relevant Arbetsyta -> ”Översikt över arbetsytan' under avsnittet Allmänt.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

När du klickar på panelen på öppnas mallen designer en serie diagram om grundläggande övervakningsdata från Azure Backup som

#### <a name="all-backup-jobs"></a>Alla säkerhetskopieringsjobb

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Återställningsjobb

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Inbyggda Azure Backup-aviseringar för Azure-resurser

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Inbyggda Azure Backup-aviseringar för lokala resurser

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Aktiva datakällor

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS Vault Molnlagring

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Ovanstående diagram tillhandahålls med hjälp av mallen och kunden är gratis att redigera/Lägg till flera diagram.

> [!IMPORTANT]
> När du distribuerar mallen skapas i stort sett ett skrivskyddat Lås och du måste ta bort den för att redigera mallen och spara. Om du vill ta bort lås, titta i fönstret ”Lås' under avsnittet” Settings ”i Log Analytics-arbetsytan.

### <a name="create-alerts-using-log-analytics"></a>Skapa aviseringar med hjälp av Log Analytics

Azure Monitor kan du skapa egna aviseringar från LA arbetsyta där du kan *utnyttja Azure-åtgärdsgrupper för att välja din önskade sätt*. Klicka på något av diagrammen ovan för att öppna avsnittet ”loggar” i LA arbetsytan ***där du kan redigera frågor och skapa aviseringar på dem***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

När du klickar på ”ny Aviseringsregel” enligt ovan öppnar skärmen Azure Monitor skapande av varning.

Som du kan se nedan, resursen har redan markerats som arbetsytan LA och åtgärden gruppen integration har angetts.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Observera att den relevanta prissättningen påverkan för att skapa den här frågan har angetts [här](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Aviseringstillståndet

Viktigaste är utlösande villkoret för aviseringen. När du klickar på ”villkor” laddas automatiskt Kusto-fråga på skärmen ”loggar” enligt nedan och du kan redigera den så att den passar ditt scenario. Vissa exempelfrågor Kusto finns i den [nedan](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Redigera Kusto-fråga om det behövs genom att markera rätt tröskelvärdet (som avgör när aviseringen bränner), rätt perioden (tidsfönster som frågan körs), och hur ofta. För till exempel: För till exempel: Om tröskelvärdet är större än 0, perioden är 5 minuter och frekvensen är 5 minuter, sedan översätts regeln som ”kör fråga var femte minut under de senaste 5 minuterna och om antalet resultat som är större än 0, meddela mig via den valda åtgärdsgruppen”

#### <a name="action-group-integration"></a>Åtgärden gruppen integration

Åtgärdsgrupper ange aviseringskanaler som är tillgängligt för användaren. Klicka på ”Skapa ny” under ”åtgärdsgrupper” visas avsnittet listan över meddelande mekanismer.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Läs mer om den [aviseringar från LA arbetsyta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och [åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) Azure Monitor-dokumentationen.

Du kan därför uppfyller alla aviseringar och Övervakningskrav från LA ensam eller använda den som en kompletterande teknik för att inbyggd meddelande mekanismer.

### <a name="sample-kusto-queries"></a>Exempelfrågor för Kusto

Standarddiagram skulle ger dig Kusto-frågor för grundläggande scenarier där du kan skapa aviseringar. Du kan också ändra dem för att visa de data som du vill att aviseringar ska visas på. Här har vissa exempelfrågor Kusto som kan du klistra in i ”loggar”-fönstret och sedan skapa en avisering på frågan.

#### <a name="all-successful-backup-jobs"></a>Alla lyckade säkerhetskopieringsjobb

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Alla misslyckade säkerhetskopieringsjobb

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Alla slutförda jobb som används för att säkerhetskopiera virtuella Azure-datorer

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

#### <a name="all-successful-sql-log-backup-jobs"></a>Alla slutförda jobb som används för att säkerhetskopiera SQL-logg

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

#### <a name="all-successful-mab-agent-backup-jobs"></a>Alla lyckade MAB-agenten säkerhetskopieringsjobb

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

### <a name="diagnostic-data-update-frequency"></a>Uppdateringsfrekvensen för diagnostikdata

Diagnostikdata från valvet är läggs in till arbetsytan LA med vissa fördröjning. Varje händelse anländer till arbetsytan LA ***med en fördröjning på 20 – 30 minuter efter att skickas från RS-valvet.***

- De inbyggda aviseringarna backup-tjänsten (över alla lösningar) skickas när de skapas. Vilket innebär att de vanligtvis visas på arbetsytan LA efter en fördröjning på 20 – 30 minuter.
- Adhoc-säkerhetskopieringsjobb och återställningsjobb (över alla lösningar) skickas så snart som de ***är slutförda***.
- Schemalagda säkerhetskopieringsjobb körs från alla lösningar (utom SQL-säkerhetskopiering) skickas så snart som de ***är slutförda***.
- För SQL-säkerhetskopiering, eftersom vi kan ha loggsäkerhetskopior 15 minuters mellanrum, för alla slutförda schemalagda säkerhetskopieringsjobb, inklusive loggar, informationen batchar och push-överfört var sjätte timme.
- All annan information, till exempel säkerhetskopieringsobjekt, princip, återställningspunkter, lagring osv över alla lösningar skickas **minst en gång per dag.**
- En ändring i konfigurationen för säkerhetskopiering, till exempel ändra principen, Redigera princip osv utlöser en säkerhetskopierad information för alla relaterade-push.

## <a name="using-rs-vaults-activity-logs"></a>Med hjälp av RS Vault aktivitetsloggar

Du kan också använda aktivitetsloggar för att få meddelande för händelser, till exempel lyckade.

> [!CAUTION]
> **Observera att detta gäller endast för Virtuella Azure-säkerhetskopieringar.** Du kan inte använda det för andra lösningar som Azure Backup Agent, SQL-säkerhetskopior i Azure, Azure filer osv.

### <a name="sign-in-into-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen och gå vidare till relevanta Azure Recovery Services-valvet och klicka på ”aktivitetsloggavsnittet” i egenskaperna.

### <a name="identify-appropriate-log-and-create-alert"></a>Ange relevant logg och skapa avisering

Använda filter som visas i följande bild för att kontrollera om du tar emot aktivitetsloggar för säkerhetskopiering. Ändras tidsintervallet om du vill visa poster.

![Aktivitetsloggar för Virtuella Azure-säkerhetskopieringar](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Du kan klicka på ”JSON” segment för att få mer information och visa det genom att kopiera klistra in den till en textredigerare. Bör visas information för valvet och de objekt som utlöste aktiviteten Logga det vill säga säkerhetskopieringsobjektet.

Klicka på ”Lägg till aktivitetsloggavisering” genererar aviseringar för alla loggar.

Du kan klicka på ”Lägg till aktivitetsloggavisering” ovan så öppnas skärmen skapande av varning som liknar skärmen för skapande av varning [enligt beskrivningen ovan](#create-alerts-using-log-analytics).

Resursen är här RS valvet själva och därför måste du upprepa samma åtgärd för alla valv som du vill att avisering via aktivitetsloggar. Villkoret har inte någon tröskelvärdet, period, frekvens eftersom det är en händelsebaserad avisering. Aviseringen utlöses när relevanta aktivitetsloggen genereras.

## <a name="recommendation"></a>Rekommendation

***Alla aviseringar som skapats från aktivitetsloggar och LA arbetsytor kan ses i Azure Monitor i fönstret ”aviseringar” till vänster.***

Avisering via aktivitetsloggar kan användas, ***Azure Backup-tjänsten rekommenderar starkt att du använder LA för övervakning av skala och inte aktivitet av följande skäl***.

- **Vissa scenarier:** Gäller endast för Virtuella Azure-säkerhetskopieringar och ska upprepas för varje RS-valvet.
- **Definition av passar:** Den schemalagda aktiviteten för säkerhetskopiering inte ryms inom de senaste definitionerna för aktivitetsloggar och överensstämmer med [diagnostikloggar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Det här leda till oväntade påverkan när data också finns via aktiviteten logg för driftshändelsekanal ändras som visade nedan.
- **Problem med aktiviteten logg för driftshändelsekanal:** Vi har flyttat till en ny modell för också aktivitetsloggar från Azure Backup på Recovery Services-valv. Flytten har tyvärr påverkas generering av aktivitetsloggar i Azure suveräna moln. Om Azure suveräna moln användare skapas/konfigurerat några aviseringar från aktivitetsloggar via Azure Monitor, skulle de inte aktiveras. I alla offentliga Azure-regioner, om en användare samlar in Recovery Services-aktivitetsloggar till Log Analytics-arbetsyta som vi redan nämnt [här](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), dessa loggar också visades inte.

Därför rekommenderas att använda Log Analytics-arbetsytan för övervakning och avisering i skala för alla Azure Backup skyddade arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

- Referera till [Log analytics-datamodell](backup-azure-log-analytics-data-model.md) att skapa egna frågor.
