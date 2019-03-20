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
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225968"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Azure ger dig möjligheten att flytta resurser till en ny resursgrupp eller prenumeration. Du kan flytta resurser via Azure portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns [flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md). 

Azure Automation-konton är en av de resurser som kan flyttas. I den här artikeln får lära du dig stegen för att flytta Automation-konton till en annan resurs eller prenumeration.

Övergripande steg för att flytta ditt Automation-konto är:

1. Ta bort dina lösningar.
2. Ta bort länken till din arbetsyta.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kör som-konton.
5. Återaktivera dina lösningar.

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till din arbetsyta från ditt Automation-konto, måste dessa lösningar tas bort från din arbetsyta:
- **Ändringsspårning och inventering**
- **Hantering av uppdateringar** 
- **Starta/Stoppa VM under belastning** 

Hitta varje lösning i din resursgrupp och välj **ta bort**. På den **ta bort resurser** bekräftar du resurser att tas bort och välj **ta bort**.

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

I Azure-portalen går du till resursgruppen och välj **övervakning** > **aviseringar** > **hantera Varningsregler**.

![Aviseringar sidan som visar valet av hantera aviseringsregler](../media/move-account/alert-rules.png)

På den **regler** bör du se en lista över de aviseringar som konfigurerats i den resursgruppen. Den **Starta/Stoppa VM** lösningen skapar tre Varningsregler:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Välj de här tre aviseringsregler och välj sedan **ta bort**. Den här åtgärden tar bort dessa Varningsregler.

![Regler för sidan som begär bekräftelse av borttagning för valda regler](../media/move-account/delete-rules.png)

> [!NOTE]
> Om du inte ser några Varningsregler på den **regler** , ändra den **Status** att visa **inaktiverad** aviseringar, eftersom du kan ha inaktiverat dem.

När varningsreglerna har tagits bort kan du ta bort åtgärdsgrupp som har skapats för den **Starta/Stoppa VM** lösning meddelanden.

I Azure-portalen väljer du **övervakaren** > **aviseringar** > **hantera åtgärdsgrupper**.

Välj **StartStop_VM_Notification** i listan. På sidan åtgärd gruppen väljer **ta bort**.

![Välj på sidan med åtgärden ta bort](../media/move-account/delete-action-group.png)

På samma sätt kan du ta bort din åtgärdsgruppen med hjälp av PowerShell med den [: Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) cmdlet, som visas i följande exempel:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länken till din arbetsyta

I Azure-portalen väljer du **automatiseringskontot** > **relaterade resurser** > **länkade arbetsytan**. Välj **ta bort arbetsytans länk** att ta bort länken till arbetsytan från ditt Automation-konto.

![Ta bort länk till en arbetsyta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta Automation-konto

När du tar bort tidigare objekt, kan du fortsätta att ta bort ditt Automation-konto och dess runbooks. Bläddra till resursgruppen för ditt Automation-konto i Azure-portalen. Välj **flytta** > **flytta till en annan prenumeration**.

![Sidan med resursgrupper, flytta till en annan prenumeration](../media/move-account/move-resources.png)

Välj resurserna i resursgruppen som du vill flytta. Se till att du inkluderar din **automatiseringskontot**, **Runbook**, och **Log Analytics-arbetsyta** resurser.

När förflyttningen har slutförts, finns det ytterligare steg som krävs för att få allt att fungera.

## <a name="re-create-run-as-accounts"></a>Återskapa kör som-konton

[Kör som-konton](../manage-runas-account.md) skapa ett tjänstobjekt i Azure Active Directory att autentisera med Azure-resurser. När du ändrar prenumerationer, används det befintliga kör som-kontot inte längre i Automation-kontot.

Gå till ditt Automation-konto i den nya prenumerationen och välj **kör som-konton** under **kontoinställningar**. Du ser att kör som-konton visas som ofullständig nu.

![Kör som-konton är ofullständiga](../media/move-account/run-as-accounts.png)

Välj Kör som-kontona. På den **egenskaper** väljer **ta bort** att ta bort kör som-kontot.

> [!NOTE]
> Om du inte har behörighet att skapa eller visa kör som-konton, visas följande meddelande: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Läs om de behörigheter som krävs för att konfigurera ett kör som-konto i [behörigheter som krävs för att konfigurera kör som-konton](../manage-runas-account.md#permissions).

När kör som-konton tas bort, väljer **skapa** under **kör som-konto**. På den **Lägg till Kör som-konto** väljer **skapa** skapa kör som-konto och tjänstens huvudnamn. Upprepa föregående steg med den **klassiska kör som-konto**.

## <a name="enable-solutions"></a>Aktivera lösningar

När du har återskapat kör som-konton, ska du återaktivera de lösningar som du har tagit bort innan flytten. Aktivera **ändringsspårning och inventering** och **uppdateringshantering**, Välj den respektive kapaciteten i ditt Automation-konto. Välj Log Analytics-arbetsytan som du har flyttat över och **aktivera**.

![Återaktivera lösningar i ditt flyttade Automation-konto](../media/move-account/reenable-solutions.png)

Datorer som är integrerat med dina lösningar kommer att vara synliga när du har anslutit den befintliga Log Analytics-arbetsytan.

Aktivera den **Starta/Stoppa VM** vid låg belastning på nätverket lösning måste du distribuera lösningen igen. Under **relaterade resurser**väljer **Starta/Stoppa VM** > **Läs mer om att aktivera lösningen** > **skapa** att starta distributionen.

På den **lägga till lösning** väljer din Log Analytics-arbetsytan och Automation-kontot.  

![Lägga till lösning meny](../media/move-account/add-solution-vm.png)

Detaljerade anvisningar om hur du konfigurerar lösningen finns i [Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Efter flytten verifiering

När förflyttningen har slutförts, kontrollerar du följande lista med aktiviteter som ska verifieras:

|Funktion|Tester|Felsökning av länk|
|---|---|---|
|Runbooks|En runbook kan har kör och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
| Källkontroll|Du kan köra en manuell synkronisering på din källkontrolldatabasen.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändringsspårning och inventering|Kontrollera att du ser aktuella inventeringsdata från dina virtuella datorer.|[Felsöka ändringsspårning](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrollera att du ser dina datorer och de är felfria.</br>Kör en programuppdateringsdistribution för testning.|[Felsöka hantering av uppdateringar](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar resurser i Azure finns [flytta resurser i Azure](../../azure-resource-manager/move-support-resources.md).
