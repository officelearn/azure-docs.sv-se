---
title: Flytta en Log Analytics arbets yta i Azure Monitor | Microsoft Docs
description: Lär dig hur du flyttar din Log Analytics-arbetsyta till en annan prenumeration eller resurs grupp.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093792"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Flytta en Log Analytics arbets yta till en annan prenumeration eller resurs grupp

I den här artikeln lär du dig hur du flyttar Log Analytics arbets ytan till en annan resurs grupp eller prenumeration i samma region. Du kan lära dig mer om att flytta Azure-resurser via Azure Portal, PowerShell, Azure CLI eller REST API. [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Du kan inte flytta en arbets yta till en annan region.

## <a name="verify-active-directory-tenant"></a>Verifiera Active Directory klient
Käll-och mål prenumerationer måste finnas i samma Azure Active Directory-klient. Använd Azure PowerShell för att kontrol lera att båda prenumerationerna har samma klient-ID.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Ta bort lösningar
Hanterade lösningar som är installerade på arbets ytan flyttas med flytt åtgärden Log Analytics-arbetsyta. Eftersom du måste ta bort länken från arbets ytan till ett Automation-konto, måste lösningar som förlitar sig på den länken tas bort.

Lösningar som måste tas bort inkluderar följande: 

- Uppdateringshantering
- Spårning av ändringar
- Starta/stoppa virtuella datorer utanför arbetstid


### <a name="azure-portal"></a>Azure Portal
Använd följande procedur för att ta bort lösningarna med hjälp av Azure Portal:

1. Öppna menyn för resurs gruppen som alla lösningar är installerade i.
2. Välj de lösningar som ska tas bort.
3. Klicka på **ta bort resurser** och bekräfta sedan de resurser som ska tas bort genom att klicka på **ta bort**.

![Ta bort lösningar](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

Om du vill ta bort lösningarna med PowerShell använder du cmdleten [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) som visas i följande exempel:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Ta bort aviserings regler
För lösningen **Starta/stoppa virtuella datorer** måste du också ta bort de aviserings regler som skapats av lösningen. Använd följande procedur i Azure Portal för att ta bort dessa regler.

1. Öppna menyn **övervakare** och välj sedan **aviseringar**.
2. Klicka på **Hantera aviserings regler**.
3. Välj följande tre varnings regler och klicka sedan på **ta bort**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Ta bort regler](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Ta bort länk för Automation-konto
Använd följande procedur för att ta bort länken till Automation-kontot från arbets ytan med hjälp av Azure Portal:

1. Öppna menyn **Automation-konton** och välj sedan det konto som ska tas bort.
2. I avsnittet **relaterade resurser** på menyn väljer du **länkad arbets yta**. 
3. Klicka på länken för att **ta bort länkar** till arbets ytan från ditt Automation-konto.

    ![Ta bort länk till arbets yta](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Flytta din arbets yta

### <a name="azure-portal"></a>Azure Portal
Använd följande procedur för att flytta din arbets yta med Azure Portal:

1. Öppna menyn **Log Analytics arbets ytor** och välj sedan din arbets yta.
2. På sidan **Översikt** klickar du på **ändra** bredvid antingen **resurs grupp** eller **prenumeration**.
3. En ny sida öppnas med en lista över resurser som är relaterade till arbets ytan. Välj de resurser som du vill flytta till samma mål prenumeration och resurs grupp som arbets ytan. 
4. Välj en mål **prenumeration** och **resurs grupp**. Om du flyttar arbets ytan till en annan resurs grupp i samma prenumeration visas inte **prenumerations** alternativet.
5. Klicka på **OK** för att flytta arbets ytan och valda resurser.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Om du vill flytta din arbets yta med PowerShell använder du [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) som i följande exempel:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Efter flyttningen bör borttagna lösningar och Automation-konto-länken konfigureras om så att arbets ytan återställs till sitt tidigare tillstånd.


## <a name="next-steps"></a>Nästa steg
- En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](../../azure-resource-manager/move-support-resources.md).
