---
title: Felsöka fel med Azure Automation delade resurser
description: Lär dig hur du felsöker problem med Azure Automation delade resurser
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848467"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Felsöka fel med delade resurser

Den här artikeln beskriver lösningar för att lösa problem som du kan köra över när du använder de delade resurserna i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="module-stuck-importing"></a>Scenario: En modul har fastnat importera

#### <a name="issue"></a>Problem

En modul har fastnat i den **importera** tillstånd när du importerar eller uppdatera dina moduler i Azure automation.

#### <a name="cause"></a>Orsak

Importera PowerShell-moduler är en komplicerad process i flera steg. Den här processen introducerar möjligheten att en modul importeras inte korrekt. Om det här problemet inträffar kan modulen som du importerar ha fastnat i ett tillfälligt tillstånd. Mer information om den här processen finns [importera en PowerShell-modul]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du ta bort den modul som har fastnat i den **importera** tillstånd med hjälp av den [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet. Du kan sedan göra om modulen importerades.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Kör som-konton

### <a name="unable-create-update"></a>Scenario: Du kan inte skapa eller uppdatera en Kör som-konto

#### <a name="issue"></a>Problem

När du försöker skapa eller uppdatera en Kör som-konto, visas ett fel som liknar följande felmeddelande visas:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver skapa eller uppdatera kör som-kontot eller resursen är låst på en resursgruppsnivå.

#### <a name="resolution"></a>Lösning

Du måste ha behörighet till de olika resurserna som används av kör som-kontot för att skapa eller uppdatera en Kör som-konto. Läs om de behörigheter som krävs för att skapa eller uppdatera en Kör som-konto i [kör som-kontobehörighet](../manage-runas-account.md#permissions).

Om problemet beror på ett lås, kontrollera att låset är ok om du vill ta bort och navigera till den resurs som är låst, högerklicka låset och välj **ta bort** att ta bort låset.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.