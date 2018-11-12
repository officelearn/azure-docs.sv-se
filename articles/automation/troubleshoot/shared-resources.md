---
title: Felsöka fel med Azure Automation delade resurser
description: Lär dig hur du felsöker problem med Azure Automation delade resurser
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263563"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Felsöka fel med delade resurser

Den här artikeln beskriver lösningar för att lösa problem som du kan köra över när du använder de delade resurserna i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="module-stuck-importing"></a>Scenario: En modul har fastnat importera

#### <a name="issue"></a>Problem

När du importerar eller uppdaterar dina moduler i Azure automation måste du hitta en modul som har fastnat i den **importera** tillstånd.

#### <a name="error"></a>Fel

Importera PowerShell-moduler är en komplicerad process i flera steg. Den här processen introducerar möjligheten att en modul importeras inte korrekt. Om detta inträffar kan ha fastnat i modulen som du importerar i ett tillfälligt tillstånd. Mer information om den här processen finns [importera en PowerShell-modul]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du ta bort den modul som har fastnat i den **importera** tillstånd med hjälp av den [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet. Du kan sedan göra om modulen importerades.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.