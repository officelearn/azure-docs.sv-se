---
title: Använda diagnostikinställningar för Recovery Services Vaults
description: En artikel som beskriver hur du använder gamla och nya diagnostikhändelser för Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672790"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Använda diagnostikinställningar för Recovery Services-valv

Azure Backup skickar diagnostikhändelser som kan samlas in och användas för analys, aviseringar och rapportering. 

Du kan konfigurera diagnostikinställningar för ett Recovery Services Vault via Azure-portalen genom att navigera till valvet och klicka på **menyalternativet Diagnostikinställningar.** Om du klickar på **+ Lägg till diagnostikinställning** kan du skicka en eller flera diagnostikhändelser till en lagringskonto, händelsehubb eller en Log Analytics -arbetsyta (LOG Analytics).

![Diagnostikinställningar Blad](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostikhändelser tillgängliga för Azure Backup-användare

Azure Backup innehåller följande diagnostiska händelser, som var och en innehåller detaljerade data på en viss uppsättning säkerhetskopieringsrelaterade artefakter:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupSkyddadeInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Datamodell för azure backup diagnostikhändelser](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Data för dessa händelser kan skickas till antingen ett lagringskonto, EN ARBETSyta eller en eventhub. Om du skickar dessa data till en LA-arbetsyta måste du välja **den resursspecifika** växlingsknappen på skärmen **Diagnostikinställning** (se mer information i avsnitten nedan).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Använda diagnostikinställningar med Log Analytics (LA)

Azure Backup anpassar sig till översikten över Azure Log Analytics och låter dig nu skicka arkivdiagnostikdata till dedikerade LA-tabeller för säkerhetskopiering. Dessa kallas [resursspecifika tabeller](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Så här skickar du dina arkivdiagnostikdata till LA:

1.    Navigera till valvet och klicka på **Diagnostikinställningar**. Klicka på **+ Lägg till diagnostikinställning**.
2.    Ge diagnostikinställningen ett namn.
3.    Markera rutan **Skicka till Logganalys** och välj en Logganalysarbetsyta.
4.    Välj **Resursspecifik** i växlingsknappen och kontrollera följande sex händelser - **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**och **AddonAzureBackupStorage**.
5.    Klicka på **Spara**.

![Resursspecifikt läge](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

När data flödar in i LA Workspace skapas dedikerade tabeller för var och en av dessa händelser på arbetsytan. Du kan fråga någon av dessa tabeller direkt och även utföra kopplingar eller unioner mellan dessa tabeller om det behövs.

> [!IMPORTANT]
> Ovanstående sex händelser, nämligen CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy och AddonAzureBackupStorage, stöds **endast** i resursspecifikt läge i [säkerhetskopieringsrapporter](https://docs.microsoft.com/azure/backup/configure-reports). **Observera att om du försöker skicka data för dessa sex händelser i Azure Diagnostics Mode, kommer inga data att visas i säkerhetskopieringsrapporter.**

## <a name="legacy-event"></a>Äldre händelse

Traditionellt har alla säkerhetskopieringsrelaterade diagnostikdata för ett valv innehållits i en enda händelse som kallas "AzureBackupReport". De sex händelserna som beskrivs ovan är i huvudsak en nedbrytning av alla data som finns i AzureBackupReport. 

För närvarande fortsätter vi att stödja AzureBackupReport-händelsen för bakåtkompatibilitet, i de fall där användare har befintliga anpassade frågor om den här händelsen, till exempel anpassade loggaviseringar, anpassade visualiseringar etc. Vi rekommenderar dock **att du flyttar till de nya [händelserna](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) så tidigt som möjligt,** eftersom det gör data mycket enklare att arbeta med i loggfrågor, ger bättre upptäckbarhet av scheman och deras struktur, förbättrar prestanda för både inmatningsfördröjning och frågetider. 

**Den äldre händelsen i Azure Diagnostics-läget kommer så småningom att vara inaktuell och därför kan det hjälpa dig att undvika komplexa migreringar vid ett senare tillfälle**. Vår [rapporteringslösning](https://docs.microsoft.com/azure/backup/configure-reports) som utnyttjar Log Analytics kommer också att sluta stödja data från den äldre händelsen.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Steg för att gå över till nya diagnostikinställningar (till Log Analytics-arbetsyta)

1. Identifiera vilka valv som skickar data till Log Analytics Workspace(s) med hjälp av den äldre händelsen och vilka prenumerationer de tillhör. Kör nedanstående arbetsytor för att identifiera dessa valv och prenumerationer:

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

2. Använd Azure Backups [inbyggda Azure-princip](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) för att lägga till en ny diagnostikinställning för alla valv i ett angivet scope. Den här principen lägger till en ny diagnostikinställning i de valv som antingen inte har en diagnostikinställning (eller) har bara en äldre diagnostikinställning. Den här principen kan tilldelas en hel prenumeration eller resursgrupp åt gången. Observera att du behöver "Ägare" åtkomst till varje prenumeration som principen är tilldelad.

Du kan välja att ha separata diagnostikinställningar för AzureBackupReport och de sex nya händelserna, tills du har migrerat alla dina anpassade frågor för att använda data från de nya tabellerna. Bilden nedan visar ett exempel på ett valv med två diagnostikinställningar. Den första inställningen med namnet **Setting1** skickar data från AzureBackupReport-händelsen till en LA-arbetsyta i AzureDiagnostics-läge. Den andra inställningen, med namnet **Setting2,** skickar data för de sex nya Azure Backup-händelserna till en LA-arbetsyta i resursspecifikt läge.

![Två inställningar](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport-händelsen stöds **endast** i Azure Diagnostics Mode. **Observera att om du försöker skicka data för den här händelsen i resursspecifikt läge kommer inga data att flöda till LA-arbetsytan.**

> [!NOTE]
> Växlingsknappen för Azure Diagnostics/Resource Specific visas bara om användaren kontrollerar **Skicka till Logganalys**. Om du vill skicka data till ett lagringskonto eller en händelsehubb kan en användare helt enkelt välja önskad destination och kontrollera någon av de önskade händelserna, utan några ytterligare indata. Återigen rekommenderas att inte välja den äldre händelsen AzureBackupReport, framöver.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Användare som skickar Azure Site Recovery Events till Log Analytics (LA)

Azure Backup och Azure Site Recovery Events skickas från samma Recovery Services Vault. Eftersom Azure Site Recovery för närvarande inte är inbyggda i resursspecifika tabeller uppmanas användare som vill skicka Azure Site Recovery Events till LA att **endast** använda Azure Diagnostics Mode (se avbildningen nedan). **Om du väljer Resursspecifikt läge för Azure Site Recovery Events förhindras att nödvändiga data skickas till LA Workspace**.

![Händelser för återställning av webbplatser](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

För att sammanfatta ovanstående nyanser:

* Om du redan har aktiverat LA-diagnostik med Azure Diagnostics och har skrivit anpassade frågor ovanpå den, behåll den inställningen **intakt**tills du migrerar dina frågor för att använda data från de nya händelserna.
* Om du också vill gå ombord på nya tabeller (som rekommenderas) skapar du en **ny** diagnostikinställning, väljer **Resursspecifik** och väljer de sex nya händelserna som anges ovan.
* Om du för närvarande skickar Azure Site Recovery Events till LA ska **du inte** välja Resursspecifikt läge för dessa händelser, annars flödar inte data för dessa händelser till din LA-arbetsyta. Skapa i stället ytterligare en **diagnostikinställning,** välj **Azure Diagnostics**och välj relevanta Azure Site Recovery-händelser.

Bilden nedan visar ett exempel på att en användare har tre diagnostikinställningar för ett valv. Den första inställningen med namnet **Setting1** skickar data från AzureBackupReport-händelsen till en LA Workspace i AzureDiagnostics-läge. Den andra inställningen, med namnet **Setting2,** skickar data från de sex nya Azure Backup-händelserna till en LA-arbetsyta i resursspecifikt läge. Den tredje inställningen, med namnet **Setting3,** skickar data från Azure Site Recovery-händelser till en LA Workspace i Azure Diagnostics Mode.

![Tre inställningar](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig logganalysdatamodellen för diagnostikhändelserna](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
