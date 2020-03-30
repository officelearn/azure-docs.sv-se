---
title: Övervaka Azure-säkerhetskopiering med Azure Monitor
description: Övervaka Azure Backup-arbetsbelastningar och skapa anpassade aviseringar med hjälp av Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459522"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Övervaka i stor skala med hjälp av Azure Monitor

Azure Backup tillhandahåller [inbyggda övervaknings- och aviseringsfunktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan ytterligare hanteringsinfrastruktur. Men den här inbyggda tjänsten är begränsad i följande scenarier:

- Om du övervakar data från flera Recovery Services-valv mellan prenumerationer
- Om den önskade meddelandekanalen *inte* är e-post
- Om användarna vill ha aviseringar för fler scenarier
- Om du vill visa information från en lokal komponent, till exempel System Center Data Protection Manager i Azure, som portalen inte visas i [**Säkerhetskopieringsjobb**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) eller [**säkerhetskopior**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Använda log analytics-arbetsyta

### <a name="create-alerts-by-using-log-analytics"></a>Skapa aviseringar med hjälp av Log Analytics

I Azure Monitor kan du skapa egna aviseringar på en Log Analytics-arbetsyta. På arbetsytan använder du *Azure-åtgärdsgrupper* för att välja önskad meddelandemekanism.

> [!IMPORTANT]
> Information om kostnaden för att skapa den här frågan finns i [Azure Monitor-priser](https://azure.microsoft.com/pricing/details/monitor/).

Öppna avsnittet **Loggar** på arbetsytan Log Analytics och skapa en fråga för dina egna loggar. När du väljer **Ny varningsregel**öppnas sidan Azure Monitor alert-creation, vilket visas i följande bild.

![Skapa en avisering på en log Analytics-arbetsyta](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Här är resursen redan markerad som Log Analytics-arbetsytan och åtgärdsgruppintegration tillhandahålls.

![Sidan Skapa aviseringsgenerering för Logganalys](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Varningsvillkor

Den definierande egenskapen för en avisering är dess utlösande tillstånd. Välj **Villkor om** du vill läsa in Kusto-frågan automatiskt på sidan **Loggar** enligt följande bild. Här kan du redigera villkoret så att det passar dina behov. Mer information finns i [Exempel på Kusto-frågor](#sample-kusto-queries).

![Ställa in ett varningsvillkor](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Om det behövs kan du redigera Kusto-frågan. Välj ett tröskelvärde, en period och en frekvens. Tröskelvärdet avgör när aviseringen ska höjas. Perioden är det tidsfönster då frågan körs. Om tröskelvärdet till exempel är större än 0 är perioden 5 minuter och frekvensen är 5 minuter, kör regeln frågan var femte minut och granskar de föregående 5 minuterna. Om antalet resultat är större än 0 meddelas du via den valda åtgärdsgruppen.

#### <a name="alert-action-groups"></a>Grupper för varningsåtgärd

Använd en åtgärdsgrupp för att ange en meddelandekanal. Om du vill visa tillgängliga meddelandemekanismer väljer du **Skapa ny**under **Åtgärdsgrupper**.

![Tillgängliga meddelandemekanismer i fönstret "Lägg till åtgärdsgrupp"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Du kan uppfylla alla varnings- och övervakningskrav enbart från Log Analytics, eller så kan du använda Log Analytics för att komplettera inbyggda meddelanden.

Mer information finns i [Skapa, visa och hantera loggaviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) och Skapa och hantera [åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

Standarddiagrammen ger dig Kusto-frågor för grundläggande scenarier där du kan skapa aviseringar. Du kan också ändra frågorna för att få de data som du vill bli aviserad på. Klistra in följande exempel på Kusto-frågor på sidan **Loggar** och skapa sedan aviseringar för frågorna:

- Alla lyckade säkerhetskopieringsjobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Alla misslyckade säkerhetskopieringsjobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Alla lyckade azure vm-säkerhetskopieringsjobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade SQL-loggsäkerhetsjobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alla lyckade Azure Backup-agentjobb

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Säkerhetskopieringslagring förbrukas per säkerhetskopieringsobjekt

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Uppdateringsfrekvens för diagnostikdata

Diagnostikdata från valvet pumpas till Log Analytics-arbetsytan med viss fördröjning. Varje händelse anländer till Log Analytics arbetsytan *20 till 30 minuter* efter att den har skjutits från Recovery Services-valvet. Här är mer information om eftersläpningen:

- Alla lösningar skjuts säkerhetskopieringstjänstens inbyggda aviseringar så fort de skapas. Så de visas vanligtvis i Log Analytics arbetsytan efter 20 till 30 minuter.
- I alla lösningar skjuts säkerhetskopieringsjobb på begäran och återställningsjobb så fort de *är klara.*
- För alla lösningar utom SQL-säkerhetskopiering skjuts schemalagda säkerhetskopieringsjobb ut så fort de *är klara.*
- För SQL-säkerhetskopiering, eftersom säkerhetskopiering av loggar kan ske var 15:e minut, batchas och skjuts information om alla slutförda schemalagda säkerhetskopieringsjobb, inklusive loggar, och skjuts in var 6:e timme.
- Alla lösningar, annan information som säkerhetskopieringsobjekt, princip, återställningspunkter, lagring och så vidare, skjuts minst *en gång per dag.*
- En ändring i konfigurationen för säkerhetskopiering (till exempel ändra princip eller redigeringsprincip) utlöser en push av all relaterad säkerhetskopieringsinformation.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Använda recovery services-valvets aktivitetsloggar

> [!CAUTION]
> Följande steg gäller endast för *Azure VM-säkerhetskopior.* Du kan inte använda dessa steg för lösningar som Azure Backup-agenten, SQL-säkerhetskopior i Azure eller Azure-filer.

Du kan också använda aktivitetsloggar för att få meddelanden om händelser som säkerhetskopiering. Börja med att följa dessa steg:

1. Logga in på Azure Portal.
1. Öppna det relevanta valvet för återställningstjänster.
1. Öppna avsnittet **Aktivitetslogg** i valvets egenskaper.

Så här identifierar du lämplig logg och skapar en avisering:

1. Kontrollera att du får aktivitetsloggar för lyckade säkerhetskopior genom att använda filtren som visas i följande bild. Ändra värdet för **Tidsspansintervall** om det behövs för att visa poster.

   ![Filtrering för att hitta aktivitetsloggar för Azure VM-säkerhetskopior](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Välj operationsnamnet om du vill visa relevant information.
1. Välj **Ny aviseringsregel** om du vill öppna sidan **Skapa regel.**
1. Skapa en avisering genom att följa stegen i [Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Ny varningsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Här är resursen själva valvet för Återställningstjänster. Upprepa samma steg för alla valv där du vill bli meddelad via aktivitetsloggar. Villkoret har ingen tröskel, period eller frekvens eftersom den här aviseringen baseras på händelser. Så snart den relevanta aktivitetsloggen genereras utlöses aviseringen.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Använda Log Analytics för att övervaka i stor skala

Du kan visa alla aviseringar som skapats från aktivitetsloggar och Log Analytics-arbetsytor i Azure Monitor. Öppna bara fönstret **Aviseringar** till vänster.

Även om du kan få meddelanden via aktivitetsloggar rekommenderar vi starkt att du använder Log Analytics i stället för aktivitetsloggar för övervakning i stor skala. Skälet är följande:

- **Begränsade scenarier**: Meddelanden via aktivitetsloggar gäller endast för Azure VM-säkerhetskopior. Meddelandena måste ställas in för varje Recovery Services-valv.
- **Definitionspassning**: Den schemalagda säkerhetskopieringsaktiviteten passar inte in i den senaste definitionen av aktivitetsloggar. I stället justeras den mot [resursloggar](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace). Den här justeringen orsakar oväntade effekter när data som flödar genom aktivitetsloggkanalen ändras.
- **Problem med aktivitetsloggkanalen**: I Recovery Services-valv följer aktivitetsloggar som pumpas från Azure Backup en ny modell. Tyvärr påverkar den här ändringen genereringen av aktivitetsloggar i Azure Government, Azure Germany och Azure China 21Vianet. Om användare av dessa molntjänster skapar eller konfigurerar aviseringar från aktivitetsloggar i Azure Monitor utlöses inte aviseringarna. Om en användare [samlar in återställningstjänster-aktivitetsloggar i en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)visas inte heller dessa loggar i alla offentliga Azure-regioner.

Använd en Log Analytics-arbetsyta för övervakning och avisering i stor skala för alla dina arbetsbelastningar som skyddas av Azure Backup.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar anpassade frågor finns i [Logganalysdatamodell](backup-azure-reports-data-model.md).
