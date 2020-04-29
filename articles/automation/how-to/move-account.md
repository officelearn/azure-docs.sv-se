---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: Den här artikeln beskriver hur du flyttar ditt Automation-konto till en annan prenumeration.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681896"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Med Azure Automation kan du flytta vissa resurser till en ny resurs grupp eller prenumeration. Du kan flytta resurser via Azure Portal, PowerShell, Azure CLI eller REST API. Mer information om processen finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Det Azure Automation kontot är en av de resurser som du kan flytta. I den här artikeln lär du dig att flytta Automation-konton till en annan resurs eller prenumeration. De övergripande stegen för att flytta ditt Automation-konto är:

1. Ta bort dina lösningar.
2. Ta bort länken till arbets ytan.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kör som-konton.
5. Återaktivera dina lösningar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till arbets ytan från ditt Automation-konto måste du ta bort dessa lösningar från arbets ytan:

- Ändringsspårning och inventering
- Uppdateringshantering
- Starta/stoppa virtuella datorer utanför arbetstid

1. Leta reda på resursgruppen på Azure-portalen.
2. Hitta varje lösning och klicka på **ta bort** på sidan ta bort resurser.

    ![Ta bort lösningar från Azure Portal](../media/move-account/delete-solutions.png)

    Om du vill kan du ta bort lösningarna med cmdleten [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Ta bort aviserings regler för lösningen starta/stoppa virtuella datorer under låg belastning

För lösningen starta/stoppa virtuella datorer under låg tid måste du också ta bort de aviserings regler som skapats av lösningen.

1. I Azure Portal går du till din resurs grupp och väljer **övervaknings** > **aviseringar** > **Hantera aviserings regler**.

![Sidan aviseringar med val av hantera aviserings regler](../media/move-account/alert-rules.png)

2. På sidan regler bör du se en lista över de aviseringar som kon figurer ATS i resurs gruppen. Lösningen skapar följande regler:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Välj reglerna en i taget och klicka på **ta bort** för att ta bort dem.

    ![Sidan regler begär bekräftelse av borttagning för markerade regler](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Om du inte ser några varnings regler på sidan regler ändrar du fältet **status** till inaktiverat för att visa inaktiverade aviseringar, eftersom du kan ha inaktiverat dem.

4. När aviserings reglerna tas bort måste du ta bort den åtgärds grupp som har skapats för att starta/stoppa virtuella datorer vid inaktive ring av lösnings meddelanden. I Azure Portal väljer du **övervaka** > **aviseringar** > **Hantera åtgärds grupper**.

5. Välj **StartStop_VM_Notification**. 

6. På sidan åtgärds grupp väljer du **ta bort**.

    ![Sidan åtgärds grupp](../media/move-account/delete-action-group.png)

    Om du vill kan du ta bort åtgärds gruppen med cmdleten [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Ta bort länk till arbets ytan

Nu kan du ta bort länken till arbets ytan:

1. I Azure Portal väljer du **Automation-konto** > **relaterade resurser** > **länkad arbets yta**. 

2. Välj **länken för att ta bort länkar** för arbets ytan från ditt Automation-konto.

    ![Ta bort länken till en arbets yta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

Nu kan du flytta ditt Automation-konto och dess Runbooks. 

1. I Azure Portal bläddrar du till resurs gruppen för ditt Automation-konto. Välj **Flytta** > **Flytta till en annan prenumeration**.

    ![Sidan resurs grupp flyttar du till en annan prenumeration](../media/move-account/move-resources.png)

2. Välj de resurser i resurs gruppen som du vill flytta. Se till att du inkluderar ditt Automation-konto, Runbooks och Log Analytics arbets ytans resurser.

## <a name="recreate-run-as-accounts"></a>Återskapa kör som-konton

[Kör som-konton](../manage-runas-account.md) skapa ett huvud namn för tjänsten i Azure Active Directory för att autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga kör som-kontot. Återskapa kör som-konton:

1. Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som-konton** under **konto inställningar**. Du ser att kör som-kontona visas som ofullständiga nu.

    ![Kör som-konton är ofullständiga](../media/move-account/run-as-accounts.png)

2. Ta bort kör som-kontona en i taget med knappen **ta bort** på sidan Egenskaper. 

    > [!NOTE]
    > Om du inte har behörighet att skapa eller Visa kör som-konton visas följande meddelande: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` information om de behörigheter som krävs för att konfigurera ett Kör som-konto finns i [behörigheter som krävs för att konfigurera kör som-konton](../manage-runas-account.md#permissions).

3. När du har tagit bort kör som-kontona väljer du **skapa** under **Kör som-konto i Azure**. 

4. På sidan Lägg till Azure kör som-konto väljer du **skapa** för att skapa kör som-kontot och tjänstens huvud namn. 

5. Upprepa stegen ovan med det klassiska kör som-kontot i Azure.

## <a name="enable-solutions"></a>Aktivera lösningar

När du har återskapat kör som-kontona måste du återaktivera de lösningar som du har tagit bort innan du flyttar: 

1. Om du vill aktivera Ändringsspårning-och inventerings lösningen väljer du Ändringsspårning och inventering i ditt Automation-konto. Välj Log Analytics arbets ytan som du flyttade över och välj **Aktivera**.

2. Upprepa steg 1 för Uppdateringshantering lösningen.

    ![Återaktivera lösningar i det flyttade Automation-kontot](../media/move-account/reenable-solutions.png)

3. Datorer som är inbyggda med dina lösningar visas när du har anslutit till den befintliga Log Analytics-arbetsytan. Om du vill aktivera lösningen starta/stoppa virtuella datorer under låg belastning måste du distribuera om lösningen. Under **relaterade resurser**väljer du **Starta/stoppa virtuella datorer** > **Läs mer om och aktiverar lösningen** > **skapa** för att starta distributionen.

4. På sidan Lägg till lösning väljer du Log Analytics arbets yta och Automation-konto.

    ![Menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

5. Konfigurera lösningen enligt beskrivningen i [lösningen starta/stoppa virtuella datorer vid inaktive ring av timmar i Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifiera flytten

När flyttningen är klar kontrollerar du att funktionerna i listan nedan är aktiverade. 

|Funktion|Tester|Felsökning|
|---|---|---|
|Runbooks|En Runbook kan köra och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering på käll kontrollens lagrings plats.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändrings spårning och inventering|Kontrol lera att du ser aktuella inventerings data från datorerna.|[Felsöka ändrings spårning](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrol lera att du ser dina datorer och att de är felfria.</br>Kör en test program uppdaterings distribution.|[Felsöka uppdaterings hantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrol lera att du ser alla dina delade resurser, till exempel [autentiseringsuppgifter](../shared-resources/credentials.md), [variabler](../shared-resources/variables.md)och liknande.|

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du flyttar resurser i Azure i [Flytta resurser i Azure](../../azure-resource-manager/management/move-support-resources.md).
