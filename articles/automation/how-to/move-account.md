---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: Den här artikeln beskriver hur du flyttar ditt Automation-konto till en annan prenumeration
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01f3995a80375f2deada13c36c48600243a17623
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733425"
---
# <a name="move-your-automation-account-to-another-subscription"></a>Flytta ditt Automation-konto till en annan prenumeration

Azure ger dig möjligheten att flytta resurser till en ny resursgrupp eller prenumeration med samma klient internt via Azure portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns [flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md). Automation-konton är en av de resurser som kan flyttas men det finns särskilda åtgärder som behövs när du flyttar ditt Automation-konto.

Övergripande steg för att flytta ditt Automation-konto till är:

* Ta bort dina lösningar
* Ta bort länken till din arbetsyta
* Flytta Automation-konto
* Ta bort och återskapa kör som-konton
* Återaktivera dina lösningar

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till din arbetsyta från ditt Automation-konto, ändringsspårning och inventering, tas hantering av uppdateringar och starta/stoppa virtuella datorer under av lösningar för timmar bort från din arbetsyta.

I resursgruppen, markerar du varje **lösning** och klicka på **ta bort**. På den **ta bort resurser** bekräftar du resurser att tas bort och klicka på **ta bort**.

![Ta bort lösningar från Azure portal](../media/move-account/delete-solutions.png)

Du kan utföra samma åtgärd med den [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) som det visas i följande exempel:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Ytterligare steg för att starta/stoppa virtuella datorer

För den **Starta/Stoppa VM** lösning, du måste också ta bort aviseringsregler som skapats av lösningen.

Navigera till din resursgrupp i Azure-portalen och välj **aviseringar** under **övervakning**. På den **aviseringar** väljer **hantera aviseringsregler**

![Aviseringar där du klickar på Hantera aviseringsregler](../media/move-account/alert-rules.png)

På den **regler** bör du se en lista över alla aviseringar som har konfigurerats i den resursgruppen. Den **Starta/Stoppa VM** lösningen skapar 3 Varningsregler

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Välj dessa 3 Varningsregler och klicka på **ta bort**. Den här åtgärden tar bort dessa Varningsregler.

![Sidan med regler som har valts och som tagits bort](../media/move-account/delete-rules.png)

> [!NOTE]
> Om du inte ser några Varningsregler på den **regler** , ändra den **Status** att visa **inaktiverad** aviseringar som du har inaktiverat dem.

När varningsreglerna tas bort, måste du ta bort åtgärdsgrupp som har skapats för meddelanden om Starta/Stoppa VM-lösning.

I Azure-portalen går du till **övervakaren**väljer **aviseringar**, och klicka på **hantera åtgärdsgrupper**.

Välj din åtgärdsgrupp i listan, får namnet **StartStop_VM_Notification**. På sidan åtgärd grupper **ta bort**

![Åtgärden sidan, klicka på Ta bort](../media/move-account/delete-action-group.png)

På samma sätt kan du ta bort din åtgärdsgrupp med PowerShell. Den här åtgärden är klar med den [: Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdleten som visas i följande exempel:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länken till din arbetsyta

I Azure-portalen går du till din **Automation-konto**. Under **relaterade resurser**, klickar du på **länkade arbetsytan**. Klicka på **ta bort arbetsytans länk** att ta bort länken till arbetsytan från ditt Automation-konto.

![Tar bort länken en arbetsyta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta Automation-konto

När alla objekt som tidigare har tagits bort kan fortsätta du att ta bort ditt Automation-konto och dess runbooks. Navigera till resursgruppen för ditt Automation-konto i Azure-portalen. Välj **flytta** och sedan **flytta till en annan prenumeration**.

![Sidan med resursgrupper väljer Flytta till en annan prenumeration](../media/move-account/move-resources.png)

Välj resurserna i resursgruppen som du vill flytta. Se till att du inkluderar din **Automatiseringskontot**, **Runbook**, och **Log Analytics-arbetsyta** resurser.

När förflyttningen har slutförts, finns det ytterligare steg som måste vidtas för att få allt att fungera.

## <a name="recreate-run-as-accounts"></a>Återskapa kör som-konton

[Kör som-konton](../manage-runas-account.md) skapa ett tjänstobjekt i Azure Active Directory att autentisera med Azure-resurser. När du ändrar prenumerationer, är det befintliga kör som-kontot inte längre användas av Automation-kontot.

Gå till ditt Automation-konto i den nya prenumerationen och välj **kör som-konton** under **kontoinställningar**. Du ser att det kör som-konton visas som ofullständig nu.

![Kör som-konton som visar som ofullständigt](../media/move-account/run-as-accounts.png)

Klicka på varje kör som-konto och på den **egenskaper** klickar du på **ta bort** att ta bort kör som-kontot.

> ! [OBS] Om du inte har behörighet att skapa eller visa kör som-konton visas följande meddelande `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`. Läs om de behörigheter som krävs för att konfigurera ett kör som-konto i [behörigheter som krävs för att konfigurera kör som-konton](../manage-runas-account.md#permissions).

När de kör som-konton tas bort, klickar du på **skapa** på den **Azure kör som-konto**. På den **Lägg till Azures kör som-konto** klickar du på **skapa** att skapa kör som-konto och tjänstens huvudnamn. Upprepa föregående steg med den **Azures klassiska kör som konto**.

## <a name="enable-solutions"></a>Aktivera lösningar

När de kör som-konton har återskapats måste du återaktivera de lösningar som du tar bort innan flytten. Aktivera **ändringsspårning och inventering** och **uppdateringshantering**, Välj den respektive kapaciteten i ditt Automation-konto. Välj Log Analytics-arbetsytan som du har flyttat över och klicka på **aktivera**.

![Återaktivera lösningar i ditt flyttade Automation-konto](../media/move-account/reenable-solutions.png)

Dina virtuella datorer som är integrerat med dina lösningar kommer att visas igen eftersom du ansluter den befintliga Log Analytics-arbetsytan.

Om du vill återaktivera Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning, måste du distribuera lösningen igen. Under **relaterade resurser**väljer **Starta/Stoppa VM**. Klicka på **Läs mer om att aktivera lösningen** och klicka på **skapa** att starta distributionen.

På den **lägga till lösning** väljer din Log Analytics-arbetsytan och Automation-kontot.  

![Kör som-konton som visar som ofullständigt](../media/move-account/add-solution-vm.png)

Detaljerade anvisningar om hur du konfigurerar lösningen finns i [Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Azure Automation](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>Efter flytten verifiering

När övergången är klar kan du se till att kontrollera olika scenarier i ditt Automation-konto för att se till att allt fungerar som förväntat. I följande tabell visas en lista med aktiviteter som ska verifieras efter flytten har slutförts:

|Funktion|Tester|Felsökning av länk|
|---|---|---|
|Runbooks|En Runbook kan har kör och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
| Källkontroll|Du kan köra en manuell synkronisering på din källkontrolldatabasen.|[Integrering av källkontroll](../source-control-integration.md)|
|Ändringsspårning och inventering|Kontrollera att du ser aktuella inventeringsdata från dina virtuella datorer.|[Felsöka ändringsspårning](../troubleshoot/change-tracking.md)|
|Uppdateringshantering|Kontrollera du se dina datorer och de är felfria</br>Kör en programuppdateringsdistribution för testning.|[Felsöka hantering av uppdateringar](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar resurser i Azure finns [flytta resurser i Azure](../../azure-resource-manager/move-support-resources.md)
