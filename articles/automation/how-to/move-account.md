---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: I den här artikeln beskrivs hur du flyttar ditt Automation-konto till en annan prenumeration.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681896"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Med Azure Automation kan du flytta vissa resurser till en ny resursgrupp eller prenumeration. Du kan flytta resurser via Azure-portalen, PowerShell, Azure CLI eller REST API. Mer information om processen finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Automation-kontot är en av de resurser som du kan flytta. I den här artikeln får du lära dig att flytta Automation-konton till en annan resurs eller prenumeration. Stegen på hög nivå för att flytta ditt Automation-konto är:

1. Ta bort dina lösningar.
2. Ta bort länken till arbetsytan.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kontona Kör som.
5. Återaktivera dina lösningar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till arbetsytan från ditt Automation-konto måste du ta bort dessa lösningar från arbetsytan:

- Ändringsspårning och inventering
- Uppdateringshantering
- Starta/stoppa virtuella datorer utanför arbetstid

1. Leta reda på resursgruppen på Azure-portalen.
2. Hitta varje lösning och klicka på **Ta bort** på sidan Ta bort resurser.

    ![Ta bort lösningar från Azure-portalen](../media/move-account/delete-solutions.png)

    Om du vill kan du ta bort lösningarna med cmdleten [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Ta bort varningsregler för start-/stopp-virtuella datorer under starttimmarslösning

För start/stop-virtuella datorer under starttimmar måste du också ta bort varningsreglerna som skapas av lösningen.

1. Gå till resursgruppen i Azure-portalen och välj **Övervaka** > aviseringar**Hantera varningsregler****Alerts** > .

![Sida med aviseringar som visar val av regler för hantera aviseringar](../media/move-account/alert-rules.png)

2. På sidan Regler bör du se en lista över de aviseringar som konfigurerats i resursgruppen. Lösningen skapar dessa regler:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Markera reglerna en i taget och klicka på **Ta bort** för att ta bort dem.

    ![Regelsida som begär bekräftelse på borttagning för valda regler](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Om du inte ser några varningsregler på sidan Regler ändrar du fältet **Status** till Inaktiverat för att visa inaktiverade aviseringar eftersom du kan ha inaktiverat dem.

4. När varningsreglerna tas bort måste du ta bort den åtgärdsgrupp som skapats för start-/stopp-virtuella datorer under lösningsmeddelanden för lediga timmar. I Azure-portalen väljer du **Övervaka** > aviseringar Hantera**åtgärdsgrupper** > **Manage action groups**.

5. Välj **StartStop_VM_Notification**. 

6. På åtgärdsgruppssidan väljer du **Ta bort**.

    ![Sidan Åtgärdsgrupp](../media/move-account/delete-action-group.png)

    Om du vill kan du ta bort din åtgärdsgrupp med cmdleten [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Ta bort länken till arbetsytan

Nu kan du ta bort länken till arbetsytan:

1. I Azure-portalen väljer du Linked**arbetsyta** **för Automation-kontorelaterade** > **resurser** > . 

2. Välj **Ta bort länkningsarbetsytan** om du vill ta bort länken till arbetsytan från ditt Automation-konto.

    ![Ta bort länken till en arbetsyta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

Nu kan du flytta ditt Automation-konto och dess runbooks. 

1. I Azure-portalen bläddrar du till resursgruppen för ditt Automation-konto. Välj **Flytta** > **flytta till en annan prenumeration**.

    ![Resursgruppssida, flytta till en annan prenumeration](../media/move-account/move-resources.png)

2. Markera de resurser i resursgruppen som du vill flytta. Se till att du inkluderar dina automationskonto, runbooks och Log Analytics arbetsyteresurser.

## <a name="recreate-run-as-accounts"></a>Återskapa kör som konton

[Kör som konton](../manage-runas-account.md) skapar ett tjänsthuvudnamn i Azure Active Directory för att autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga Run As-kontot. Så här återskapar du kontona Kör som:

1. Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som konton** under **Kontoinställningar**. Du ser att kör som-kontona visas som ofullständiga nu.

    ![Kör som konton är ofullständiga](../media/move-account/run-as-accounts.png)

2. Ta bort kontona Kör som en i taget med knappen **Ta bort** på sidan Egenskaper. 

    > [!NOTE]
    > Om du inte har behörighet att skapa eller visa kontona Kör `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` som visas följande meddelande: Mer information om de behörigheter som krävs för att konfigurera ett Run As-konto finns [i Behörigheter som krävs för att konfigurera Kör som konton](../manage-runas-account.md#permissions).

3. När du har tagit bort kontona Kör som väljer du **Skapa** under **Azure Kör som konto**. 

4. På sidan Lägg till Azure Kör som konto väljer du **Skapa** för att skapa huvudhuvudbeloppet för kör som-kontot och tjänsten. 

5. Upprepa stegen ovan med Azure Classic Run As-kontot.

## <a name="enable-solutions"></a>Aktivera lösningar

När du har återskapat kontona Kör som måste du återaktivera de lösningar som du tog bort före flytten: 

1. Om du vill aktivera lösningen Ändra spårning och lager väljer du Ändra spårning och lager i ditt Automation-konto. Välj arbetsytan Logganalys som du flyttade över och välj **Aktivera**.

2. Upprepa steg 1 för lösning för uppdateringshantering.

    ![Återaktivera lösningar i ditt flyttade Automation-konto](../media/move-account/reenable-solutions.png)

3. Datorer som är inbyggda med dina lösningar visas när du har anslutit den befintliga Log Analytics-arbetsytan. Om du vill aktivera start-/stopp-start-/start-lösningen under avtidslösning måste du distribuera om lösningen. Under **Relaterade resurser**väljer du **Start/Stop virtuella datorer** > **Läs mer om och aktivera lösningen** > **Skapa** för att starta distributionen.

4. På sidan Lägg till lösning väljer du arbetsytan Log Analytics och Automation-kontot.

    ![Menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

5. Konfigurera lösningen enligt beskrivningen i [Start/Stop-virtuella datorer under starttimmarslösning i Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifiera flytten

När flytten är klar kontrollerar du att funktionerna nedan är aktiverade. 

|Funktion|Tester|Felsökning|
|---|---|---|
|Runbooks|En runbook kan köras och ansluta till Azure-resurser.|[Felsöka runbook-flöden](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering i källkontrolldatabasen.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändringsspårning och lager|Kontrollera att du ser aktuella inventeringsdata från dina datorer.|[Felsöka spårning av ändringar](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrollera att du ser dina datorer och att de är felfria.</br>Kör en testprogramuppdateringsdistribution.|[Felsöka uppdateringshantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrollera att du ser alla delade resurser, till exempel [autentiseringsuppgifter,](../shared-resources/credentials.md) [variabler](../shared-resources/variables.md)och liknande.|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar resurser i Azure finns [i Flytta resurser i Azure](../../azure-resource-manager/management/move-support-resources.md).
