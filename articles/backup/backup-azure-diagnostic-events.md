---
title: Använda diagnostikinställningar för Recovery Services valv
description: En artikel som beskriver hur du använder de gamla och nya diagnostikloggar för Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 7abf8873aafeb996476d818376057bfd8732d906
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583953"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Använda diagnostikinställningar för Recovery Services-valv

Azure Backup skickar diagnostiska händelser som kan samlas in och användas i syfte att analysera, Avisera och rapportera. 

Du kan konfigurera diagnostikinställningar för ett Recovery Services valv via Azure Portal genom att navigera till valvet och klicka på meny alternativet **diagnostikinställningar** . Genom att klicka på **+ Lägg till diagnostisk inställning** kan du skicka en eller flera diagnostiska händelser till ett lagrings konto, en Event Hub-eller en Log Analytics (La)-arbets yta.

![Bladet inställningar för diagnostik](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostiska händelser som är tillgängliga för Azure Backup användare

Azure Backup innehåller följande diagnostiska händelser, som innehåller detaljerade data om en speciell uppsättning säkerhetsrelaterade artefakter:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[Data modell för Azure Backup Diagnostics-händelser](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Data för dessa händelser kan skickas till antingen ett lagrings konto, en LA-arbets yta eller en Event Hub. Om du skickar dessa data till en LA-arbets yta, måste du välja den **resurs information** som ska växlas på skärmen **diagnostik inställning** (mer information finns i avsnitten nedan).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Använda diagnostikinställningar med Log Analytics (LA)

Genom att justera med Azure Log Analytics-översikten Azure Backup nu kan du skicka valv diagnostikdata till dedikerade LA tabeller för säkerhets kopiering. Dessa kallas för [resursbaserade tabeller](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Skicka dina valv diagnostikdata till LA:

1.  Navigera till valvet och klicka på **diagnostiska inställningar**. Klicka på **+ Lägg till diagnostisk inställning**.
2.  Ange ett namn på den diagnostiska inställningen.
3.  Markera kryss rutan **Skicka till Log Analytics** och välj en Log Analytics arbets yta.
4.  Välj **resurs Special** i växla och kontrol lera följande sex händelser – **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**och **AddonAzureBackupStorage**.
5.  Klicka på **Spara**.

![Resurs speciellt läge](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

När data flödar in på arbets ytan LA skapas dedikerade tabeller för var och en av dessa händelser på arbets ytan. Du kan fråga någon av dessa tabeller direkt och även utföra kopplingar eller unioner mellan dessa tabeller om det behövs.

> [!IMPORTANT]
> Ovanstående sex händelser, nämligen CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy och AddonAzureBackupStorage, stöds **bara** i resurs särskilt läge. **Observera att om du försöker skicka data för de här sex händelserna i Azure-diagnostik läge kommer inga data att flöda till arbets ytan LA.**

## <a name="legacy-event"></a>Äldre händelse

Traditionellt har alla säkerhetskopierade diagnostikdata för ett valv funnits i en enskild händelse med namnet "AzureBackupReport". De sex händelser som beskrivs ovan är i själva verket en nedbrytning av alla data som finns i AzureBackupReport. 

För närvarande fortsätter vi att stödja AzureBackupReport-händelsen för bakåtkompatibilitet, i de fall där användare har befintliga anpassade frågor om den här händelsen, till exempel anpassade logg aviseringar, anpassade visualiseringar osv. Vi rekommenderar dock att du väljer nya händelser för alla nya diagnostikinställningar i valvet eftersom detta gör att det blir mycket enklare att arbeta med logg frågor, vilket ger bättre identifiering av scheman och deras struktur, förbättrar prestandan för båda inmatningarna svars tid och fråge tider. Stöd för att använda Azure-diagnostik läge kommer slutligen att gå ut och därmed välja nya händelser kan du undvika komplexa migreringar vid ett senare tillfälle.

Du kan välja att skapa separata diagnostikinställningar för AzureBackupReport och de sex nya händelserna tills du har migrerat alla anpassade frågor för att använda data från de nya tabellerna. Bilden nedan visar ett exempel på ett valv med två diagnostikinställningar. Den första inställningen med namnet **Setting1** skickar data för AzureBackupReport-händelsen till en La-arbetsyta i AzureDiagnostics-läge. Den andra inställningen, med namnet **Setting2** , skickar data från de sex nya Azure Backup händelser till en La-arbetsyta i resurs särskilt läge.

![Två inställningar](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport-händelsen stöds **bara** i Azure-diagnostik läge. **Observera att om du försöker skicka data för den här händelsen i ett resurs särskilt läge kommer inga data att flöda till arbets ytan LA.**

> [!NOTE]
> Växlings fönstret för Azure-diagnostik/resurs visas bara om användaren kontrollerar **Skicka till Log Analytics**. Om du vill skicka data till ett lagrings konto eller en Händelsehubben, kan en användare bara välja det begärda målet och kontrol lera alla önskade händelser utan ytterligare indata. Återigen rekommenderar vi att du inte väljer den äldre Event AzureBackupReport, går vidare.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Användare som skickar Azure Site Recovery händelser till Log Analytics (LA)

Azure Backup och Azure Site Recovery händelser skickas från samma Recovery Services-valv. Eftersom Azure Site Recovery för närvarande inte har registrerats på resursbaserade tabeller, dirigeras användare som vill skicka Azure Site Recovery händelser till LA till att **endast** använda Azure-diagnostik läge (se bilden nedan). **Om du väljer resurs speciellt läge för Azure Site Recovery händelser så förhindras att nödvändiga data skickas till arbets ytan La**.

![Site Recovery händelser](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Så här sammanfattar du ovanstående olika delarna:

* Om du redan har lagt till val av diagnostik med Azure-diagnostik och har skrivna anpassade frågor ovanpå den, behåller du inställningen **intakt**, tills du migrerar dina frågor för att använda data från de nya händelserna.
* Om du även vill publicera på nya tabeller (som rekommenderas) skapar du en **ny** diagnostisk inställning, väljer **resurs specifik** och väljer de sex nya händelserna som anges ovan.
* Om du för närvarande skickar Azure Site Recovery händelser till LA ska du **inte** välja resurs speciellt läge för dessa händelser, annars kommer inte data för dessa händelser att flöda till din La-arbetsyta. Skapa i stället en **ytterligare diagnostisk inställning**, Välj **Azure-diagnostik**och välj de relevanta Azure Site Recovery händelser.

Bilden nedan visar ett exempel på en användare som har tre diagnostikinställningar för ett valv. Den första inställningen med namnet **Setting1** skickar data från AzureBackupReport-händelsen till en La-arbetsyta i AzureDiagnostics-läge. Den andra inställningen, med namnet **Setting2** , skickar data från de sex nya Azure Backup händelser till en La-arbetsyta i resurs särskilt läge. Den tredje inställningen med namnet **Setting3**skickar data från Azure Site Recovery händelser till en La-arbetsyta i Azure-diagnostik läge.

![Tre inställningar](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Log Analytics data modell för diagnostiska händelser](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
