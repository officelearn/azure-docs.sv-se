---
title: Stöd för Az-moduler i Azure Automation
description: Den här artikeln innehåller information om hur du använder Az-moduler i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638000"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för Az-moduler i Azure Automation

Azure Automation stöder användningen av [Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. Den samlade Az-modulen importeras inte automatiskt i nya eller befintliga Automation-konton. 

## <a name="considerations"></a>Överväganden

Det finns flera saker att ta hänsyn till när du använder Az-modulerna i Azure Automation:

* Lösningar på högre nivå i ditt Automation-konto kan använda runbooks och moduler. Därför kan redigering av runbooks eller uppgraderingsmoduler potentiellt orsaka problem med dina lösningar. Du bör testa alla runbooks och lösningar noggrant i ett separat Automation-konto innan du importerar nya Az-moduler. 

* Eventuella ändringar av moduler kan påverka [Start/Stop-lösningen](automation-solution-vm-management.md) negativt. 

* Om du importerar en Az-modul i ditt Automation-konto importeras inte modulen automatiskt i PowerShell-sessionen som runbooks använder. Moduler importeras till PowerShell-sessionen i följande situationer:

    * När en runbook anropar en cmdlet från en modul
    * När en runbook importerar `Import-Module` modulen uttryckligen med cmdlet
    * När en runbook importerar en annan modul beroende på modulen

> [!NOTE]
> Vi rekommenderar inte att du ändrar moduler och runbooks i Automation-konton som innehåller några lösningar. Den här bestämmelsen är inte specifik för Az-modulerna. Det bör beaktas när du inför eventuella ändringar i ditt Automation-konto.

> [!IMPORTANT]
> Kontrollera att runbooks i ett Automation-konto importerar antingen Az-moduler eller [AzureRM-moduler,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) men inte båda, till en PowerShell-session. Om en runbook importerar Az-moduler före AzureRM-moduler slutförs runbooken. Ett fel som refererar [till Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet visas dock i jobbströmmarna och cmdlets kanske inte körs korrekt. Om runbooken importerar AzureRM-moduler före Az-moduler slutförs även runbooken. I det här fallet visas dock ett fel i jobbströmmarna som anger att både Az och AzureRM inte kan importeras i samma session eller användas i samma runbook.

## <a name="migrating-to-az-modules"></a>Migrera till Az-moduler

Vi rekommenderar att du testar en migrering till Az-moduler i ett testautomatiseringskonto. När du har skapat kontot kan du använda instruktionerna i det här avsnittet för att arbeta med modulerna.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Stoppa och avmarkera alla runbooks som använder AzureRM-moduler

För att säkerställa att du inte kör några befintliga runbooks som använder AzureRM-moduler, stoppa och avmarkera alla berörda runbooks. Du kan se vilka scheman som finns och vilka scheman som ska tas bort genom att köra kod som liknar det här exemplet:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att säkerställa att du kan boka om det i framtiden för dina runbooks, om det behövs.

### <a name="import-the-az-modules"></a>Importera Az-modulerna

>[!NOTE]
>Låt dina runbooks importera endast krävs Az moduler. Importera inte den samlade Az-modulen, eftersom den innehåller alla Az-moduler. Den här vägledningen är densamma för alla moduler.

[Modulen Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende för de andra Az-modulerna. Därför måste dina runbooks importera den här modulen till ditt Automation-konto innan du importerar andra moduler.

Så här importerar du modulerna i Azure-portalen:

1. Välj **Moduler** under **Delade resurser**i ditt Automation-konto . 
2. Klicka på **Bläddra i galleri** för att öppna sidan Bläddra i galleri.  
3. I sökfältet anger du modulnamnet, `Az.Accounts`till exempel . 
4. På sidan PowerShell-modul klickar du på **Importera** för att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här importprocessen kan också göras via [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen för import. När du har hittat modulen väljer du den, väljer fliken **Azure Automation** och klickar på Distribuera till **Azure Automation**.

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Testa dina runbooks

När du har importerat Az-modulerna till Automation-kontot kan du börja redigera dina runbooks för att använda modulerna. Majoriteten av cmdlets har samma namn som för AzureRM-modulen, förutom att AzureRM-prefixet (eller AzureRm) har ändrats till Az. En lista över moduler som inte följer den här namngivningskonventionen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Ett sätt att testa ändringen av en runbook för att `Enable-AzureRmAlias -Scope Process` använda de nya cmdlets är genom att använda i början av runbook. Genom att lägga till det här kommandot i runbooken kan skriptet köras utan ändringar.

## <a name="after-migration-details"></a>Information om eftermigrering

När migreringen är klar ska du inte försöka starta runbooks med AzureRM-moduler på Automation-kontot. Vi rekommenderar också att du inte importerar eller uppdaterar AzureRM-moduler på kontot. Tänk på kontot migreras till Az, och arbeta med Az moduler bara. 

När du skapar ett nytt Automation-konto är de befintliga AzureRM-modulerna fortfarande installerade. Du kan fortfarande uppdatera självstudien runbooks med AzureRM cmdlets. Du bör dock inte köra dessa runbooks.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Az-moduler finns i [Komma igång med Az-modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
