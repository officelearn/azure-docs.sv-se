---
title: Med hjälp av Az-moduler i Azure Automation
description: Den här artikeln innehåller information med hjälp av Az-moduler i Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a076c924d57aadfae477a5df0d128aad8e67af60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305425"
---
# <a name="az-module-support-in-azure-automation"></a>Stöd för AZ-modulen i Azure Automation

Azure automation har stöd för möjligheten att använda den [Az för Azure Powershell-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) i dina runbooks. Az-modulen importeras inte automatiskt i alla nya eller befintliga Automation-konton. Den här artikeln beskrivs hur du använder Az-moduler med Azure Automation.

## <a name="considerations"></a>Överväganden

Det finns många saker att tänka på när du använder Az-modulen i Azure Automation. Runbooks och moduler kan användas av på högre nivå lösningar i ditt Automation-konto. Redigera runbooks eller uppgradera moduler kan eventuellt medföra problem med dina runbooks. Du bör testa alla runbooks och lösningar noggrant i ett separat Automation-konto innan du importerar det nya `Az` moduler. Ändringar av moduler kan påverka på högre nivå-lösningar som uppdateringshantering och starta/stoppa VM under belastning. Vi rekommenderar att du inte ändra moduler och runbooks i Automation-konton som innehåller några lösningar. Det här beteendet är inte specifikt för Az-moduler. Det här beteendet ska beaktas när du introducerar ändringar till ditt Automation-konto.

Importera en `Az` modul i ditt Automation-konto inte automatiskt importera modulen i PowerShell-session med runbooks. Moduler som importeras till PowerShell-sessionen i följande situationer:

* När en cmdlet från en modul anropas från en runbook
* När en runbook importerar den uttryckligen med den `Import-Module` cmdlet
* När en annan modul beroende på modulen importeras till en PowerShell-session

> [!IMPORTANT]
> Det är viktigt att se till att runbooks i ett Automation-konto som antingen bara importera `Az` eller `AzureRM` moduler i PowerShell-sessioner som används av runbooks och inte båda. Om `Az` importeras innan `AzureRM` i en runbook kommer att slutföra runbook, men en [fel som refererar till get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) visas i jobbströmmar och cmdlets kan inte har korrekt köra. Om du importerar `AzureRM` och sedan `Az` din runbook fortfarande klar, men du kommer att se ett fel i jobbströmmar om att båda `Az` och `AzureRM` kan inte importeras i samma session eller används i samma runbook.

## <a name="migrating-to-az-modules"></a>Migrera till Az-moduler

Vi rekommenderar att du testar migreringen till med Az moduler i stället för AzureRM-moduler i ett test Automation-konto. När det Automation-kontot har skapats, kan du använda följande steg för att säkerställa migreringen går smidigt:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Stoppa och unschedule alla runbook som använder AzureRM-moduler

Så att du inte kör några befintliga runbooks med `AzureRM` cmdlet: ar, bör du stoppa och unschedule alla runbooks som använder `AzureRM` moduler. Du kan se vilka scheman finns och vilka scheman som måste tas bort genom att köra följande exempel:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Det är viktigt att granska varje schema separat för att säkerställa att du kan schemalägga om det i framtiden för dina runbooks om det behövs.

### <a name="import-the-az-modules"></a>Importera modulerna som Az

Importera bara Az-moduler som krävs för dina runbooks. Importera inte samlade `Az` modulen, eftersom den innehåller alla de `Az.*` moduler som ska importeras. Den här vägledningen är densamma för alla moduler.

Den [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modulen är ett beroende för den andra `Az.*` moduler. Därför måste den här modulen importeras till ditt Automation-konto innan du importerar andra moduler.

Från ditt Automation-konto väljer **moduler** under **delade resurser**. Klicka på **Bläddringsgalleriegenskapen** att öppna den **Bläddringsgalleriegenskapen** sidan.  I sökfältet, anger du modulens namn (till exempel `Az.Accounts`). På sidan PowerShell-modulen **importera** att importera modulen till ditt Automation-konto.

![Importera moduler från Automation-konto](media/az-modules/import-module.png)

Den här importen kan även göras via den [PowerShell-galleriet](https://www.powershellgallery.com) genom att söka efter modulen. När du har hittat modulen, markera den och under den **Azure Automation** fliken **distribuera till Azure Automation**.

![Importera moduler direkt från galleriet](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testa dina runbooks

När den `Az` importeras moduler i ditt Automation-konto, du kan nu börja redigera dina runbooks för att använda Az-modulen i stället. Flesta av cmdletarna som har samma namn utom för `AzureRM` har ändrats till `Az`. En lista med moduler som inte följer den här processen finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names).

Ett sätt att testa runbooks innan du ändrar din runbook för att använda de nya cmdletarna är med hjälp av `Enable-AzureRMAlias -Scope Process` i början av en runbook. Genom att lägga till det i din runbook, kan din runbook köras utan ändringar.

## <a name="after-migration-details"></a>Efter information om migrering

När migreringen är klar kan inte starta runbooks med hjälp av `AzureRM` moduler för kontot längre. Det rekommenderas också inte importera eller uppdatera `AzureRM` moduler på det här kontot. Från och med nu, Överväg att det här kontot migreras till `Az`, och fungerar med `Az` moduler endast. När ett nytt Automation-konto skapas den befintliga `AzureRM` moduler fortfarande ska installeras och självstudierunbook fortfarande vara författade med `AzureRM` cmdletar. Dessa runbooks bör inte köras.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Az-moduler finns [komma igång med Az modulen](/powershell/azure/get-started-azureps?view=azps-1.1.0).
