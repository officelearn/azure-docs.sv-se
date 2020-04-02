---
title: Stöd för Az-moduler i Azure Automation
description: Den här artikeln innehåller information om hur du använder Az-moduler i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548346"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för Az-moduler i Azure Automation

Azure Automation stöder användningen av [Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. Az-modulen importeras inte automatiskt i nya eller befintliga Automation-konton. 

## <a name="considerations"></a>Överväganden

Det finns många saker att ta hänsyn till när du använder Az-modulen i Azure Automation. Runbooks och moduler kan användas av lösningar på högre nivå i ditt Automation-konto. Om du redigerar runbooks eller uppgraderar moduler kan det orsaka problem med runbooks. Du bör testa alla runbooks och lösningar noggrant i `Az` ett separat Automation-konto innan du importerar de nya modulerna. Eventuella ändringar av moduler kan påverka [Start/Stop-lösningen](automation-solution-vm-management.md) negativt. Vi rekommenderar inte att du ändrar moduler och runbooks i Automation-konton som innehåller några lösningar. Det här beteendet är inte specifikt för Az-modulerna. Detta bör beaktas när du inför eventuella ändringar i ditt Automation-konto.

Om du `Az` importerar en modul i ditt Automation-konto importeras inte modulen automatiskt i PowerShell-sessionen som runbooks använder. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en cmdlet från en modul anropas från en runbook
* När en runbook importerar `Import-Module` den uttryckligen med cmdlet
* När en annan modul beroende på modulen importeras till en PowerShell-session

> [!IMPORTANT]
> Det är viktigt att se till att runbooks `Az` `AzureRM` i ett Automation-konto antingen bara importera eller moduler till PowerShell-sessioner som används av runbooks och inte båda. Om `Az` importeras `AzureRM` tidigare i en runbook slutförs runbooken, men ett fel som refererar [till Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet visas i jobbströmmarna och cmdlets kanske inte körs korrekt. Om du `AzureRM` importerar och sedan `Az`slutförs runbooken fortfarande, men du får `Az` `AzureRM` ett felmeddelande i jobbströmmarna som anger att båda och inte kan importeras i samma session eller användas i samma runbook.

## <a name="migrating-to-az-modules"></a>Migrera till Az-moduler

Vi rekommenderar att du testar migreringen till Az-moduler i ett testautomatiseringskonto. När automationskontot har skapats kan du använda instruktionerna i det här avsnittet för att arbeta med modulerna.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Stoppa och avmarkera alla runbooks som använder AzureRM-cmdlets

Om du vill vara säkra på att `AzureRM` du inte kör några befintliga runbooks som använder cmdlets bör du stoppa och avmarkera alla runbooks som använder `AzureRM` moduler. Du kan se vilka scheman som finns och vilka scheman som måste tas bort genom att köra kod som liknar det här exemplet.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att säkerställa att du kan schemalägga om det i framtiden för dina runbooks om det behövs.

### <a name="import-the-az-modules"></a>Importera Az-modulerna

Importera bara Az-modulerna som krävs för dina runbooks. Importera inte `Az` sammanslagningsmodulen eftersom den `Az.*` innehåller alla moduler. Den här vägledningen är densamma för alla moduler.

Modulen [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende `Az.*` för de andra modulerna. Därför måste den här modulen importeras till ditt Automation-konto innan du importerar andra moduler.

Välj **Moduler** under **Delade resurser**i ditt Automation-konto . Klicka på **Bläddra i galleri** för att öppna sidan Bläddra i **galleri.**  I sökfältet anger du modulnamnet `Az.Accounts`(till exempel ). På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här importprocessen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen för import. När du har hittat modulen väljer du den och klickar på **Distribuera till Azure Automation**under fliken Azure **Automation** .

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testa dina runbooks

När `Az` modulerna har importerats till ditt Automation-konto kan du börja redigera dina runbooks för att använda Az-modulerna. Majoriteten av cmdlets har samma namn `AzureRM` förutom att `Az`har ändrats till . En lista över moduler som inte följer den här namngivningskonventionen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa ändringen av en runbook för att `Enable-AzureRMAlias -Scope Process` använda de nya cmdlets är genom att använda i början av runbook. Genom att lägga till det här kommandot i runbooken kan skriptet köras utan ändringar.

## <a name="after-migration-details"></a>Information om eftermigrering

När migreringen är klar ska du inte `AzureRM` försöka starta runbooks med moduler på Automation-kontot längre. Det rekommenderas också att inte `AzureRM` importera eller uppdatera moduler på kontot. Tänk på att `Az`kontot har `Az` migrerats till och endast arbeta med moduler. 

När ett nytt Automation-konto `AzureRM` skapas är de befintliga modulerna fortfarande installerade. Du kan fortfarande uppdatera självstudien runbooks med `AzureRM` cmdlets. Du bör inte köra dessa runbooks.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Az-moduler finns i [Komma igång med Az-modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
