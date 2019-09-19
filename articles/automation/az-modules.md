---
title: Använda AZ-moduler i Azure Automation
description: Den här artikeln innehåller information med AZ-moduler i Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f81c0affb78d5944b8ba910cccfa0be655f1a6f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097940"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för AZ-modul i Azure Automation

Azure Automation stöder möjligheten att använda [Azure PowerShell-modulen AZ](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. AZ-modulen importeras inte automatiskt i nya eller befintliga Automation-konton. Den här artikeln beskriver hur du använder AZ-moduler med Azure Automation.

## <a name="considerations"></a>Överväganden

Det finns många saker att tänka på när du använder AZ-modulen i Azure Automation. Runbooks och moduler kan användas av lösningar på högre nivå i ditt Automation-konto. Att redigera Runbooks eller uppgradera moduler kan eventuellt orsaka problem med dina runbooks. Du bör testa alla Runbooks och lösningar noggrant i ett separat Automation-konto innan du importerar `Az` de nya modulerna. Eventuella ändringar i moduler kan negativt [Starta/stoppa-](automation-solution-vm-management.md) lösningen. Vi rekommenderar att du inte ändrar moduler och Runbooks i Automation-konton som innehåller några lösningar. Detta beteende är inte särskilt för AZ-modulerna. Det här beteendet bör beaktas när du inför ändringar i ditt Automation-konto.

Om du `Az` importerar en modul i ditt Automation-konto importeras modulen inte automatiskt i PowerShell-sessionen som används av Runbooks. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en cmdlet från en modul anropas från en Runbook
* När en Runbook importerar den explicit med `Import-Module` cmdleten
* När en annan modul beroende på modulen importeras till en PowerShell-session

> [!IMPORTANT]
> Det är viktigt att se till att Runbooks i ett Automation-konto antingen bara `Az` importerar `AzureRM` eller modulerar till PowerShell-sessioner som används av Runbooks och inte båda. Om `Az` importer ATS före `AzureRM` i en Runbook slutförs runbooken, men ett fel som [refererar till get_SerializationSettings-metoden](troubleshoot/runbooks.md#get-serializationsettings) visas i jobb strömmarna och cmdletarna kanske inte har körts korrekt. Om du importerar `AzureRM` `Az` och Runbook fortfarande kommer att slutföras, men du ser ett fel i jobb strömmar som talar om att både `Az` och `AzureRM` inte kan importeras i samma session eller används i samma Runbook.

## <a name="migrating-to-az-modules"></a>Migrera till AZ-moduler

Vi rekommenderar att du testar migreringen för att använda AZ-moduler i stället för AzureRM-moduler i ett test Automation-konto. När Automation-kontot har skapats kan du använda följande steg för att se till att migreringen går smidigt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Stoppa och ta bort schemat för alla Runbook som använder AzureRM-moduler

För att se till att du inte kör några befintliga Runbooks som `AzureRM` använder cmdlets bör du stoppa och ta bort schemat för alla Runbooks `AzureRM` som använder moduler. Du kan se vilka scheman som finns och vilka scheman som måste tas bort genom att köra följande exempel:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att se till att du kan schemalägga om det i framtiden för dina runbooks om det behövs.

### <a name="import-the-az-modules"></a>Importera AZ-modulerna

Importera endast de AZ-moduler som krävs för dina runbooks. Importera inte modulen Rollup `Az` eftersom den innehåller alla `Az.*` moduler som ska importeras. Den här vägledningen är samma för alla moduler.

[AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) -modulen är ett beroende för de andra `Az.*` modulerna. Därför måste den här modulen importeras till ditt Automation-konto innan du importerar andra moduler.

Från ditt Automation-konto väljer du **moduler** under **delade resurser**. Klicka på **Bläddra Galleri** för att öppna sidan **Bläddra i galleriet** .  I Sök fältet anger du modulnamnet (till exempel `Az.Accounts`). På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här import processen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen. När du har hittat modulen väljer du den och klickar på **Azure Automation distribuera**på fliken **Azure Automation** .

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testa dina runbooks

`Az` När modulerna har importer ATS till ditt Automation-konto kan du nu börja redigera dina runbooks för att använda modulen AZ i stället. Majoriteten av cmdletarna har samma namn, men `AzureRM` har ändrats till. `Az` En lista över moduler som inte följer den här processen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa dina runbooks innan du ändrar din Runbook till att använda de nya cmdletarna är `Enable-AzureRMAlias -Scope Process` genom att använda i början av en Runbook. Genom att lägga till detta i din Runbook kan din Runbook köras utan ändringar.

## <a name="after-migration-details"></a>Efter information om migreringen

När migreringen är klar startar du inte Runbooks med `AzureRM` hjälp av moduler på kontot längre. Vi rekommenderar också att du inte importerar eller `AzureRM` uppdaterar moduler på det här kontot. Från och med `Az` nu bör du överväga att det här `Az`kontot migreras till och att endast använda moduler. När ett nytt Automation-konto skapas, kommer `AzureRM` befintliga moduler fortfarande att installeras och själv studie Runbooks kommer fortfarande att skapas med `AzureRM` cmdletar. Dessa Runbooks ska inte köras.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder AZ-moduler finns i [komma igång med AZ-modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
