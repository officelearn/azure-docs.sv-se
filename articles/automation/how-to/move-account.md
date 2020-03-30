---
title: Flytta ditt Azure Automation-konto till en annan prenumeration
description: I den här artikeln beskrivs hur du flyttar ditt Automation-konto till en annan prenumeration
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969833"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Flytta ditt Azure Automation-konto till en annan prenumeration

Azure ger dig möjlighet att flytta vissa resurser till en ny resursgrupp eller prenumeration. Du kan flytta resurser via Azure-portalen, PowerShell, Azure CLI eller REST API. Mer information om processen finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Automation-konton är en av de resurser som kan flyttas. I den här artikeln får du lära dig stegen för att flytta Automation-konton till en annan resurs eller prenumeration.

Stegen på hög nivå för att flytta ditt Automation-konto är:

1. Ta bort dina lösningar.
2. Ta bort länken till arbetsytan.
3. Flytta Automation-kontot.
4. Ta bort och återskapa kontona Kör som.
5. Återaktivera dina lösningar.

## <a name="remove-solutions"></a>Ta bort lösningar

Om du vill ta bort länken till arbetsytan från ditt Automation-konto måste dessa lösningar tas bort från arbetsytan:
- **Ändringsspårning och inventering**
- **Uppdateringshantering**
- **Starta/stoppa virtuella datorer under lediga timmar**

Leta reda på varje lösning i resursgruppen och välj **Ta bort**. På sidan **Ta bort resurser** bekräftar du vilka resurser som ska tas bort och väljer Ta **bort**.

![Ta bort lösningar från Azure-portalen](../media/move-account/delete-solutions.png)

Du kan utföra samma uppgift med cmdleten [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) som visas i följande exempel:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Ytterligare steg för start-/stopp-virtuella datorer

För **start/stop-start-/start-/virtuella datorer** måste du också ta bort de varningsregler som skapas av lösningen.

Gå till resursgruppen i Azure-portalen och välj **Övervaka** > aviseringar**Hantera varningsregler****Alerts** > .

![Sida med aviseringar som visar val av regler för hantera aviseringar](../media/move-account/alert-rules.png)

På sidan **Regler** bör du se en lista över de aviseringar som konfigurerats i resursgruppen. Lösningen **Start/Stop VMs** skapar tre varningsregler:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Markera dessa tre varningsregler och välj sedan **Ta bort**. Den här åtgärden tar bort dessa varningsregler.

![Regelsida som begär bekräftelse på borttagning för valda regler](../media/move-account/delete-rules.png)

> [!NOTE]
> Om du inte ser några varningsregler på sidan **Regler** ändrar du **status** för att visa **inaktiverade** aviseringar eftersom du kan ha inaktiverat dem.

När varningsreglerna tas bort tar du bort åtgärdsgruppen som skapades för **lösningsmeddelandena Start/Stop.**

I Azure-portalen väljer du **Övervaka** > aviseringar Hantera**åtgärdsgrupper** > **Manage action groups**.

Välj **StartStop_VM_Notification** i listan. På åtgärdsgruppssidan väljer du **Ta bort**.

![Åtgärdsgruppsida, välj ta bort](../media/move-account/delete-action-group.png)

På samma sätt kan du ta bort din åtgärdsgrupp med hjälp av PowerShell med cmdleten [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) vilket visas i följande exempel:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Ta bort länken till arbetsytan

I Azure-portalen väljer du Linked**arbetsyta** **för Automation-kontorelaterade** > **resurser** > . Välj **Ta bort länkningsarbetsytan** om du vill ta bort länken till arbetsytan från ditt Automation-konto.

![Ta bort länken till en arbetsyta från ett Automation-konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Flytta ditt Automation-konto

När du har tagit bort de tidigare objekten kan du fortsätta att ta bort ditt Automation-konto och dess runbooks. I Azure-portalen bläddrar du till resursgruppen för ditt Automation-konto. Välj **Flytta** > **flytta till en annan prenumeration**.

![Resursgruppssida, flytta till en annan prenumeration](../media/move-account/move-resources.png)

Markera de resurser i resursgruppen som du vill flytta. Se till att du inkluderar dina **automationskonto-,** **Runbook-** och **Log Analytics-arbetsyteresurser.**

När flytten är klar, det finns ytterligare steg som krävs för att få allt att fungera.

## <a name="re-create-run-as-accounts"></a>Återskapa kör som konton

[Kör som konton](../manage-runas-account.md) skapar ett tjänsthuvudnamn i Azure Active Directory för att autentisera med Azure-resurser. När du ändrar prenumerationer använder Automation-kontot inte längre det befintliga Run As-kontot.

Gå till ditt Automation-konto i den nya prenumerationen och välj **Kör som konton** under **Kontoinställningar**. Du ser att kör som-kontona visas som ofullständiga nu.

![Kör som konton är ofullständiga](../media/move-account/run-as-accounts.png)

Välj varje Run As-konto. På sidan **Egenskaper** väljer du **Ta bort** om du vill ta bort kontot Kör som.

> [!NOTE]
> Om du inte har behörighet att skapa eller visa kontona Kör som `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` visas följande meddelande: Mer information om de behörigheter som krävs för att konfigurera ett Run As-konto finns [i Behörigheter som krävs för att konfigurera Kör som konton](../manage-runas-account.md#permissions).

När kontona Kör som har tagits bort väljer du **Skapa** under **Azure Kör som konto**. På sidan **Lägg till Azure Kör som konto** väljer du **Skapa** för att skapa huvudhuvudbeloppet för kör som-kontot och tjänsten. Upprepa föregående steg med **Azure Classic Run As-kontot**.

## <a name="enable-solutions"></a>Aktivera lösningar

När du har återskapat kontona Kör som ska du återaktivera de lösningar som du tog bort före flytten. Om du vill aktivera **Ändra spårning och lager-** och **uppdateringshantering**väljer du respektive funktion i ditt Automation-konto. Välj arbetsytan Logganalys som du flyttade över och välj **Aktivera**.

![Återaktivera lösningar i ditt flyttade Automation-konto](../media/move-account/reenable-solutions.png)

Datorer som är inbyggda med dina lösningar visas när du har anslutit den befintliga Log Analytics-arbetsytan.

Om du vill aktivera **start-/stopp-virtuella datorer** under ledig tid måste du distribuera om lösningen. Under **Relaterade resurser**väljer du **Start/Stop virtuella datorer** > **Läs mer om och aktivera lösningen** > **Skapa** för att starta distributionen.

På sidan **Lägg till lösning** väljer du ditt Log Analytics Workspace- och Automation-konto.

![Menyn Lägg till lösning](../media/move-account/add-solution-vm.png)

Detaljerade instruktioner om hur du konfigurerar lösningen finns i [Start/Stop-virtuella datorer under ledig tid-lösning i Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verifiering efter flytten

När flytten är klar kontrollerar du följande lista över uppgifter som ska verifieras:

|Funktion|Tester|Felsöka länk|
|---|---|---|
|Runbooks|En runbook kan köras och ansluta till Azure-resurser.|[Felsöka runbooks](../troubleshoot/runbooks.md)
|Källkontroll|Du kan köra en manuell synkronisering på källkontrollrepo.|[Källkontrollsintegrering](../source-control-integration.md)|
|Ändringsspårning och lager|Kontrollera att du ser aktuella inventeringsdata från dina datorer.|[Felsöka spårning av ändringar](../troubleshoot/change-tracking.md)|
|Hantering av uppdateringar|Kontrollera att du ser dina maskiner och de är friska.</br>Kör en testprogramuppdateringsdistribution.|[Felsöka uppdateringshantering](../troubleshoot/update-management.md)|
|Delade resurser|Kontrollera att du ser alla delade resurser, till exempel [autentiseringsuppgifter,](../shared-resources/credentials.md) [variabler](../shared-resources/variables.md)osv.|

## <a name="next-steps"></a>Nästa steg

Mer information om hur du flyttar resurser i Azure finns [i Flytta resurser i Azure](../../azure-resource-manager/management/move-support-resources.md).
