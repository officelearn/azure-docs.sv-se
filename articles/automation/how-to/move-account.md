---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: Den här artikeln beskriver hur du flyttar ditt Automation-konto till en annan prenumeration
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969833"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Azure ger dig möjlighet att flytta vissa resurser till en ny resurs grupp eller prenumeration. Du kan flytta resurser via Azure Portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Automation-konton är en av de resurser som kan flyttas. I den här artikeln lär du dig hur du flyttar Automation-konton till en annan resurs eller prenumeration.

De övergripande stegen för att flytta ditt Automation-konto är:

1. Ta bort dina lösningar.
2. Ta bort länken till arbets ytan.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kör som-konton.
5. Återaktivera dina lösningar.

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till arbets ytan från ditt Automation-konto måste du ta bort dessa lösningar från arbets ytan:
- **Ändringsspårning och inventering**
- **Hantering av uppdateringar**
- **Starta/stoppa virtuella datorer under några timmar**

Leta upp varje lösning i resurs gruppen och välj **ta bort**. På sidan **ta bort resurser** bekräftar du de resurser som ska tas bort och väljer **ta bort**.

![Ta bort lösningar från Azure Portal](../media/move-account/delete-solutions.png)

Du kan utföra samma aktivitet med cmdleten [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) som visas i följande exempel:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Ytterligare steg för att starta/stoppa virtuella datorer

För lösningen **Starta/stoppa virtuella datorer** måste du också ta bort de aviserings regler som skapats av lösningen.

I Azure Portal går du till din resurs grupp och väljer **övervaka** > **aviseringar** > **Hantera aviserings regler**.

![Sidan aviseringar med val av hantera aviserings regler](../media/move-account/alert-rules.png)

På sidan **regler** bör du se en lista över de aviseringar som kon figurer ATS i resurs gruppen. Lösningen **Starta/stoppa virtuella datorer** skapar tre varnings regler:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Välj dessa tre varnings regler och välj sedan **ta bort**. Den här åtgärden tar bort dessa aviserings regler.

![Sidan regler begär bekräftelse av borttagning för markerade regler](../media/move-account/delete-rules.png)

> [!NOTE]
> Om du inte ser några varnings regler på sidan **regler** ändrar du **statusen** för att visa **inaktiverade** aviseringar, eftersom du kan ha inaktiverat dem.

När aviserings reglerna tas bort tar du bort den åtgärds grupp som skapades för meddelanden **om att starta/stoppa VM-** lösningar.

I Azure Portal väljer du **övervaka** > **aviseringar** > **Hantera åtgärds grupper**.

Välj **StartStop_VM_Notification** i listan. På sidan åtgärds grupp väljer du **ta bort**.

![Sidan åtgärds grupp väljer du ta bort](../media/move-account/delete-action-group.png)

På samma sätt kan du ta bort åtgärds gruppen med hjälp av PowerShell med cmdleten [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) , som visas i följande exempel:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länk till arbets ytan

I Azure Portal väljer du **Automation-konto** > **relaterade resurser** > **länkad arbets yta**. Välj **länken för att ta bort länkar** för arbets ytan från ditt Automation-konto.

![Ta bort länken till en arbets yta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

När du har tagit bort föregående objekt kan du fortsätta att ta bort ditt Automation-konto och dess Runbooks. I Azure Portal bläddrar du till resurs gruppen för ditt Automation-konto. Välj **flytta** > **Flytta till en annan prenumeration**.

![Sidan resurs grupp flyttar du till en annan prenumeration](../media/move-account/move-resources.png)

Välj de resurser i resurs gruppen som du vill flytta. Se till att du inkluderar ditt **Automation-konto**, din **Runbook**och **Log Analytics arbets ytans** resurser.

När flyttningen är klar finns det ytterligare steg som krävs för att allt ska fungera.

## <a name="re-create-run-as-accounts"></a>Återskapa kör som-konton

[Kör som-konton](../manage-runas-account.md) skapa ett huvud namn för tjänsten i Azure Active Directory för att autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga kör som-kontot.

Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som-konton** under **konto inställningar**. Du ser att kör som-kontona visas som ofullständiga nu.

![Kör som-konton är ofullständiga](../media/move-account/run-as-accounts.png)

Välj varje kör som-konto. På sidan **Egenskaper** väljer du **ta bort** för att ta bort kör som-kontot.

> [!NOTE]
> Om du inte har behörighet att skapa eller Visa kör som-konton visas följande meddelande: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` om du vill veta mer om de behörigheter som krävs för att konfigurera ett Kör som-konto, se [behörigheter som krävs för att konfigurera kör som-konton](../manage-runas-account.md#permissions).

När kör som-kontona har tagits bort väljer du **skapa** under **Kör som-konto i Azure**. På sidan **Lägg till Azure kör som-konto** väljer du **skapa** för att skapa kör som-kontot och tjänstens huvud namn. Upprepa föregående steg med det **klassiska kör som-kontot i Azure**.

## <a name="enable-solutions"></a>Aktivera lösningar

När du har återskapat kör som-kontona återaktiverar du de lösningar som du tog bort innan du flyttar igen. Aktivera **ändringsspårning och inventering** och **uppdateringshantering**genom att välja respektive funktion i ditt Automation-konto. Välj den Log Analytics arbets ytan du flyttade över och välj **Aktivera**.

![Återaktivera lösningar i det flyttade Automation-kontot](../media/move-account/reenable-solutions.png)

Datorer som är inbyggda med dina lösningar visas när du har anslutit till den befintliga Log Analytics-arbetsytan.

Om du vill aktivera lösningen för att **Starta/stoppa virtuella datorer** vid låg belastnings tider måste du distribuera om lösningen. Under **relaterade resurser**väljer du **Starta/stoppa virtuella datorer** > **Lär dig mer om och aktiverar lösningen** > **skapa** för att starta distributionen.

På sidan **Lägg till lösning** väljer du Log Analytics arbets yta och Automation-konto.

![Menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

Detaljerade anvisningar om hur du konfigurerar lösningen finns [i starta/stoppa virtuella datorer när de inte används lösning i Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verifiering efter flytt

När flyttningen är klar kontrollerar du följande lista över aktiviteter som ska verifieras:

|Kapacitet|Tester|Fel söknings länk|
|---|---|---|
|Runbooks|En Runbook kan köra och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering på din lagrings platsen för käll kontroll.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändrings spårning och inventering|Kontrol lera att du ser aktuella inventerings data från datorerna.|[Felsöka ändrings spårning](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrol lera att du ser dina datorer och att de är felfria.</br>Kör en test program uppdaterings distribution.|[Felsöka uppdaterings hantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrol lera att du ser alla dina delade resurser, till exempel [autentiseringsuppgifter](../shared-resources/credentials.md), [variabler](../shared-resources/variables.md)osv.|

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du flyttar resurser i Azure i [Flytta resurser i Azure](../../azure-resource-manager/management/move-support-resources.md).
