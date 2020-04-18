---
title: Använda diagnostikinställningar för Recovery Services-valv
description: I den här artikeln beskrivs hur du använder gamla och nya diagnostikhändelser för Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617301"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Använda diagnostikinställningar för Recovery Services-valv

Azure Backup skickar diagnostikhändelser som kan samlas in och användas för analys, aviseringar och rapportering.

Du kan konfigurera diagnostikinställningar för ett Recovery Services-valv via Azure-portalen genom att gå till valvet och välja **diagnostikinställningar**. Om du väljer **+ Lägg till diagnostikinställning** kan du skicka en eller flera diagnostikhändelser till ett lagringskonto, en händelsenav eller en Log Analytics-arbetsyta.

![Fönstret Diagnostikinställningar](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostikhändelser tillgängliga för Azure Backup-användare

Azure Backup innehåller följande diagnostikhändelser. Varje händelse innehåller detaljerade data om en viss uppsättning säkerhetskopieringsrelaterade artefakter:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupSkyddadeInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Mer information finns i [Datamodell för Diagnostikhändelser för Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

Data för dessa händelser kan skickas till antingen ett lagringskonto, en Log Analytics-arbetsyta eller en händelsehubb. Om du skickar dessa data till en Log Analytics-arbetsyta väljer du den **resursspecifika** växlingsknappen på skärmen **Diagnostikinställningar.** Mer information finns i följande avsnitt.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Använda diagnostikinställningar med Log Analytics

Du kan nu använda Azure Backup för att skicka arkivdiagnostikdata till dedikerade Log Analytics-tabeller för säkerhetskopiering. Dessa tabeller kallas [resursspecifika tabeller](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Så här skickar du diagnostikdata till Log Analytics:

1. Gå till valvet och välj **Diagnostikinställningar**. Välj **+ Lägg till diagnostikinställning**.
1. Ge diagnostikinställningen ett namn.
1. Markera kryssrutan **Skicka till logganalys** och välj en Log Analytics-arbetsyta.
1. Välj **Resursspecifik** i växlingsknappen och välj följande sex händelser: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**och **AddonAzureBackupProtectedInstance**.
1. Välj **Spara**.

   ![Resursspecifikt läge](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

När data flödar in på log analytics-arbetsytan skapas dedikerade tabeller för var och en av dessa händelser på arbetsytan. Du kan fråga någon av dessa tabeller direkt. Du kan också utföra kopplingar eller fackföreningar mellan dessa tabeller om det behövs.

> [!IMPORTANT]
> De sex händelserna, nämligen CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage och AddonAzureBackupProtectedInstance, stöds *endast* i det resursspecifika läget i [säkerhetskopieringsrapporter](https://docs.microsoft.com/azure/backup/configure-reports). *Om du försöker skicka data för dessa sex händelser i Azure-diagnostikläge visas inga data i säkerhetskopieringsrapporter.*

## <a name="legacy-event"></a>Äldre händelse

Traditionellt fanns alla säkerhetskopieringsrelaterade diagnostikdata för ett valv i en enda händelse som kallas AzureBackupReport. De sex händelser som beskrivs här är i huvudsak en nedbrytning av alla data som finns i AzureBackupReport. 

För närvarande fortsätter vi att stödja AzureBackupReport-händelsen för bakåtkompatibilitet i fall där användare har befintliga anpassade frågor om den här händelsen. Exempel är anpassade loggaviseringar och anpassade visualiseringar. *Vi rekommenderar att du flyttar till de [nya händelserna](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) så tidigt som möjligt.* De nya händelserna:

- Gör data mycket enklare att arbeta med i loggfrågor.
- Ge bättre upptäckbarhet av scheman och deras struktur.
- Förbättra prestanda för både inmatningsfördröjning och frågetider. 

*Den äldre händelsen i Azure-diagnostikläge kommer så småningom att vara inaktuell. Om du väljer de nya händelserna kan du undvika komplexa migreringar vid ett senare tillfälle.* Vår [rapporteringslösning](https://docs.microsoft.com/azure/backup/configure-reports) som använder Log Analytics kommer också att sluta stödja data från den äldre händelsen.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Steg för att gå över till nya diagnostikinställningar för en Log Analytics-arbetsyta

1. Identifiera vilka valv som skickar data till Log Analytics-arbetsytorna med hjälp av den äldre händelsen och de prenumerationer de tillhör. Kör följande arbetsytor för att identifiera dessa valv och prenumerationer.

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

1. Använd den [inbyggda Azure-principen](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) i Azure Backup för att lägga till en ny diagnostikinställning för alla valv i ett angivet scope. Den här principen lägger till en ny diagnostikinställning i valv som antingen inte har en diagnostikinställning eller bara har en äldre diagnostikinställning. Den här principen kan tilldelas en hel prenumeration eller resursgrupp åt gången. Du måste ha ägarbehörighet till varje prenumeration som principen har tilldelats för.

Du kan välja att ha separata diagnostikinställningar för AzureBackupReport och de sex nya händelserna tills du har migrerat alla dina anpassade frågor för att använda data från de nya tabellerna. Följande bild visar ett exempel på ett valv som har två diagnostikinställningar. Den första inställningen, med namnet **Setting1,** skickar data för en AzureBackupReport-händelse till en Log Analytics-arbetsyta i Azure-diagnostikläge. Den andra inställningen, med namnet **Setting2,** skickar data om de sex nya Azure Backup-händelserna till en Log Analytics-arbetsyta i resursspecifikt läge.

![Två inställningar](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport-händelsen stöds *endast* i Azure-diagnostikläge. *Om du försöker skicka data för den här händelsen i resursspecifikt läge flödar inga data till log analytics-arbetsytan.*

> [!NOTE]
> Växlingsknappen för **Azure-diagnostik** eller **Resursspecifik** visas bara om användaren väljer **Skicka till Logganalys**. Om du vill skicka data till ett lagringskonto eller en händelsenav markerar en användare den önskade destinationen och markerar kryssrutorna för någon av de önskade händelserna, utan några ytterligare indata. Återigen rekommenderar vi att du inte väljer den äldre händelsen AzureBackupReport framöver.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Skicka Azure Site Recovery-händelser till Log Analytics

Azure Backup- och Azure Site Recovery-händelser skickas från samma Recovery Services-valv. Azure Site Recovery är för närvarande inte tillgängligt för resursspecifika tabeller. Användare som vill skicka Azure Site Recovery-händelser till Log Analytics uppmanas *att endast*använda Azure-diagnostikläge, som visas i avbildningen. *Om du väljer det resursspecifika läget för Azure Site Recovery-händelser förhindrar du att nödvändiga data skickas till log analytics-arbetsytan*.

![Platsåterställningshändelser](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Sammanfattning:

* Om du redan har konfigurerat Log Analytics-diagnostik med Azure Diagnostics och har skrivit anpassade frågor ovanpå den, behåller du inställningen *intakt* tills du migrerar dina frågor för att använda data från de nya händelserna.
* Om du också vill gå ombord på nya tabeller, som vi rekommenderar, skapar du en **ny** diagnostikinställning, väljer **Resursspecifik**och väljer de sex nya händelserna.
* Om du för närvarande skickar Azure Site Recovery-händelser till Log Analytics *ska du inte* välja det resursspecifika läget för dessa händelser. Annars flödar inte data för dessa händelser till din Log Analytics-arbetsyta. Skapa i stället ytterligare en diagnostikinställning, välj **Azure-diagnostik**och välj relevanta Azure Site Recovery-händelser.

Följande bild visar ett exempel på en användare som har tre diagnostikinställningar för ett valv. Den första inställningen, med namnet **Setting1,** skickar data från en AzureBackupReport-händelse till en Log Analytics-arbetsyta i Azure-diagnostikläge. Den andra inställningen, med namnet **Setting2,** skickar data från de sex nya Azure Backup-händelserna till en Log Analytics-arbetsyta i resursspecifikt läge. Den tredje inställningen, med namnet **Setting3,** skickar data från Azure Site Recovery-händelser till en Log Analytics-arbetsyta i Azure-diagnostikläge.

![Tre inställningar](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig logganalysdatamodellen för diagnostikhändelserna](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
