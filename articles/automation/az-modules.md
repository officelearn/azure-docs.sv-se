---
title: Använda Az-moduler i Azure Automation
description: Den här artikeln innehåller information med Az-moduler i Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986112"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för Az-moduler i Azure Automation

Azure automation stöder möjligheten att använda [Azure Powershell Az-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. Az-modulen importeras inte automatiskt i nya eller befintliga Automation-konton. I den här artikeln beskrivs hur du använder Az-moduler med Azure Automation.

## <a name="considerations"></a>Överväganden

Det finns många saker att ta hänsyn till när du använder Az-modulen i Azure Automation. Runbooks och moduler kan användas av lösningar på högre nivå i ditt Automation-konto. Om du redigerar runbooks eller uppgraderar moduler kan det orsaka problem med runbooks. Du bör testa alla runbooks och lösningar noggrant i `Az` ett separat Automation-konto innan du importerar de nya modulerna. Eventuella ändringar av moduler kan påverka [Start/Stop-lösningen](automation-solution-vm-management.md) negativt. Vi rekommenderar inte att du ändrar moduler och runbooks i Automation-konton som innehåller några lösningar. Det här beteendet är inte specifikt för Az-modulerna. Detta bör beaktas när du inför eventuella ändringar i ditt Automation-konto.

Om du `Az` importerar en modul i ditt Automation-konto importeras inte modulen automatiskt i PowerShell-sessionen som runbooks använder. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en cmdlet från en modul anropas från en runbook
* När en runbook importerar `Import-Module` den uttryckligen med cmdlet
* När en annan modul beroende på modulen importeras till en PowerShell-session

> [!IMPORTANT]
> Det är viktigt att se till att runbooks `Az` `AzureRM` i ett Automation-konto antingen bara importera eller moduler till PowerShell-sessioner som används av runbooks och inte båda. Om `Az` importeras `AzureRM` tidigare i en runbook slutförs runbooken, men ett [fel som refererar till get_SerializationSettings-metoden](troubleshoot/runbooks.md#get-serializationsettings) visas i jobbströmmarna och cmdlets kanske inte har utförts korrekt. Om du `AzureRM` importerar och sedan `Az` din runbook fortfarande kommer att slutföras, `Az` men `AzureRM` du kommer att se ett fel i jobbströmmarna som anger att båda och inte kan importeras i samma session eller användas i samma runbook.

## <a name="migrating-to-az-modules"></a>Migrera till Az-moduler

Vi rekommenderar att du testar migreringen till att använda Az-moduler i stället för AzureRM-moduler i ett testautomatiseringskonto. När automationskontot har skapats kan du använda följande steg för att säkerställa att migreringen går smidigt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Stoppa och avmarkera alla runbook som använder AzureRM-moduler

Om du vill vara säkra på att `AzureRM` du inte kör några befintliga runbooks som använder cmdlets bör du stoppa och avmarkera alla runbooks som använder `AzureRM` moduler. Du kan se vilka scheman som finns och vilka scheman som måste tas bort genom att köra följande exempel:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att säkerställa att du kan boka om det i framtiden för dina runbooks om det behövs.

### <a name="import-the-az-modules"></a>Importera Az-modulerna

Importera bara Az-modulerna som krävs för dina runbooks. Importera inte den samlade `Az` modulen eftersom den `Az.*` innehåller alla moduler som ska importeras. Den här vägledningen är densamma för alla moduler.

Modulen [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende `Az.*` för de andra modulerna. Därför måste den här modulen importeras till ditt Automation-konto innan du importerar andra moduler.

Välj **Moduler** under **Delade resurser**i ditt Automation-konto . Klicka på **Bläddra i galleri** för att öppna sidan Bläddra i **galleri.**  I sökfältet anger du modulnamnet `Az.Accounts`(till exempel ). På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här importprocessen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen. När du har hittat modulen väljer du den och klickar på **Distribuera till Azure Automation**under fliken Azure **Automation** .

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testa dina runbooks

När `Az` modulerna har importerats i ditt Automation-konto kan du nu börja redigera dina runbooks för att använda Az-modulen i stället. Majoriteten av cmdlets har samma namn `AzureRM` med undantag `Az`för har ändrats till . En lista över moduler som inte följer den här processen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa runbooks innan du ändrar runbooken för `Enable-AzureRMAlias -Scope Process` att använda de nya cmdleterna är att använda i början av en runbook. Genom att lägga till detta i runbooken kan runbooken köras utan ändringar.

## <a name="after-migration-details"></a>Efter migreringsinformation

När migreringen är klar ska du `AzureRM` inte starta runbooks med moduler på kontot längre. Det rekommenderas också att inte importera `AzureRM` eller uppdatera moduler på det här kontot. Från och med nu, anser `Az`att det `Az` här kontot har migrerats till och fungerar endast med moduler. När ett nytt Automation-konto `AzureRM` skapas kommer de befintliga modulerna fortfarande att installeras `AzureRM` och självstudiekörningarna kommer fortfarande att skapas med cmdlets. Dessa runbooks bör inte köras.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Az-moduler finns i [Komma igång med Az-modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
