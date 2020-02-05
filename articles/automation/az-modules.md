---
title: Använda Az-moduler i Azure Automation
description: Den här artikeln innehåller information med AZ-moduler i Azure Automation
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986112"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för AZ-modul i Azure Automation

Azure Automation stöder möjligheten att använda [Azure PowerShell-modulen AZ](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. AZ-modulen importeras inte automatiskt i nya eller befintliga Automation-konton. Den här artikeln beskriver hur du använder AZ-moduler med Azure Automation.

## <a name="considerations"></a>Överväganden

Det finns många saker att tänka på när du använder AZ-modulen i Azure Automation. Runbooks och moduler kan användas av lösningar på högre nivå i ditt Automation-konto. Att redigera Runbooks eller uppgradera moduler kan eventuellt orsaka problem med dina runbooks. Du bör testa alla Runbooks och lösningar noggrant i ett separat Automation-konto innan du importerar de nya `Az`-modulerna. Eventuella ändringar i moduler kan påverka [Start-/stopp](automation-solution-vm-management.md) lösningen negativt. Vi rekommenderar inte att du ändrar moduler och Runbooks i Automation-konton som innehåller några lösningar. Detta beteende är inte särskilt för AZ-modulerna. Det här beteendet bör beaktas när du inför ändringar i ditt Automation-konto.

Om du importerar en `Az`-modul i ditt Automation-konto importeras modulen inte automatiskt i PowerShell-sessionen som används av Runbooks. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en cmdlet från en modul anropas från en Runbook
* När en Runbook importerar den explicit med `Import-Module`-cmdleten
* När en annan modul beroende på modulen importeras till en PowerShell-session

> [!IMPORTANT]
> Det är viktigt att se till att Runbooks i ett Automation-konto antingen bara importerar `Az` eller `AzureRM` moduler till PowerShell-sessioner som används av Runbooks och inte båda. Om `Az` importeras innan `AzureRM` i en Runbook, slutförs runbooken, men ett fel som [refererar till get_SerializationSettings-metoden](troubleshoot/runbooks.md#get-serializationsettings) visas i jobb strömmarna och cmdletarna kanske inte har körts korrekt. Om du importerar `AzureRM` och sedan `Az` din Runbook fortfarande är slutförd, men du ser ett fel i jobb strömmarna som talar om att både `Az` och `AzureRM` inte kan importeras i samma session eller används i samma Runbook.

## <a name="migrating-to-az-modules"></a>Migrera till AZ-moduler

Vi rekommenderar att du testar migreringen för att använda AZ-moduler i stället för AzureRM-moduler i ett test Automation-konto. När Automation-kontot har skapats kan du använda följande steg för att se till att migreringen går smidigt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Stoppa och ta bort schemat för alla Runbook som använder AzureRM-moduler

För att säkerställa att du inte kör några befintliga Runbooks som använder `AzureRM`-cmdlets bör du stoppa och ta bort schemat för alla Runbooks som använder `AzureRM` moduler. Du kan se vilka scheman som finns och vilka scheman som måste tas bort genom att köra följande exempel:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att se till att du kan schemalägga om det i framtiden för dina runbooks om det behövs.

### <a name="import-the-az-modules"></a>Importera AZ-modulerna

Importera endast de AZ-moduler som krävs för dina runbooks. Importera inte sammanslagnings `Az`-modulen eftersom den innehåller alla `Az.*`-moduler som ska importeras. Den här vägledningen är samma för alla moduler.

[AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) -modulen är ett beroende för de andra `Az.*` modulerna. Därför måste den här modulen importeras till ditt Automation-konto innan du importerar andra moduler.

Från ditt Automation-konto väljer du **moduler** under **delade resurser**. Klicka på **Bläddra Galleri** för att öppna sidan **Bläddra i galleriet** .  I Sök fältet anger du modulnamnet (till exempel `Az.Accounts`). På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här import processen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen. När du har hittat modulen väljer du den och klickar på **Azure Automation distribuera**på fliken **Azure Automation** .

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testa dina runbooks

När `Az` modulerna har importer ATS i ditt Automation-konto kan du nu börja redigera dina runbooks för att använda modulen AZ i stället. Majoriteten av cmdletarna har samma namn, förutom att `AzureRM` har ändrats till `Az`. En lista över moduler som inte följer den här processen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa dina runbooks innan du ändrar din Runbook till att använda de nya cmdletarna är genom att använda `Enable-AzureRMAlias -Scope Process` i början av en Runbook. Genom att lägga till detta i din Runbook kan din Runbook köras utan ändringar.

## <a name="after-migration-details"></a>Efter information om migreringen

När migreringen är klar startar du inte Runbooks med hjälp av `AzureRM` moduler på kontot längre. Vi rekommenderar också att du inte importerar eller uppdaterar `AzureRM` moduler på det här kontot. Från och med nu bör du överväga att det här kontot ska migreras till `Az`och endast köras med `Az` moduler. När ett nytt Automation-konto skapas kommer befintliga `AzureRM`-moduler fortfarande att installeras och själv studie Runbooks kommer fortfarande att skapas med `AzureRM`-cmdletar. Dessa Runbooks ska inte köras.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder AZ-moduler finns i [komma igång med AZ-modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
