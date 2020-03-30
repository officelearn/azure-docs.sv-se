---
title: Flytta en log analytics-arbetsyta i Azure Monitor | Microsoft-dokument
description: Läs om hur du flyttar arbetsytan Log Analytics till en annan prenumeration eller resursgrupp.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659500"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Flytta en Log Analytics-arbetsyta till en annan prenumeration eller resursgrupp

I den här artikeln får du lära dig stegen för att flytta Log Analytics-arbetsytan till en annan resursgrupp eller prenumeration i samma region. Du kan läsa mer om hur du flyttar Azure-resurser via Azure-portalen, PowerShell, Azure CLI eller REST API. på [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Du kan inte flytta en arbetsyta till en annan region.

## <a name="verify-active-directory-tenant"></a>Verifiera Active Directory-klient
Arbetsytans källa och målprenumerationer måste finnas i samma Azure Active Directory-klientorganisation. Använd Azure PowerShell för att verifiera att båda prenumerationerna har samma klient-ID.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Arbetsyteflyttningsöverväganden
Hanterade lösningar som är installerade på arbetsytan flyttas med flyttan Log Analytics workspace. Anslutna agenter förblir anslutna och behåller skicka data till arbetsytan efter flytten. Eftersom flyttåtgärden kräver att det inte finns någon länk från arbetsytan till något automatiseringskonto, måste lösningar som är beroende av den länken tas bort.

Lösningar som måste tas bort innan du kan ta bort länken till ditt automatiseringskonto:

- Uppdateringshantering
- Spårning av ändringar
- Starta/stoppa virtuella datorer utanför arbetstid


### <a name="delete-in-azure-portal"></a>Ta bort i Azure Portal
Använd följande procedur för att ta bort lösningarna med Azure-portalen:

1. Öppna menyn för resursgruppen som alla lösningar är installerade i.
2. Välj de lösningar som ska tas bort.
3. Klicka på **Ta bort resurser** och bekräfta sedan de resurser som ska tas bort genom att klicka på Ta **bort**.

![Ta bort lösningar](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Ta bort med PowerShell

Om du vill ta bort lösningarna med PowerShell använder du cmdleten [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) enligt följande exempel:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Ta bort varningsregler
För **start/stop-start-/start-/virtuella datorer** måste du också ta bort de varningsregler som skapas av lösningen. Använd följande procedur i Azure-portalen för att ta bort dessa regler.

1. Öppna **Monitor-menyn** och välj sedan **Aviseringar**.
2. Klicka på **Hantera varningsregler**.
3. Markera följande tre varningsregler och klicka sedan på **Ta bort**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Ta bort regler](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Ta bort länken Automation-konto
Använd följande procedur för att ta bort länken till Automation-kontot från arbetsytan med Hjälp av Azure-portalen:

1. Öppna menyn **Automation-konton** och välj sedan det konto som ska tas bort.
2. I avsnittet **Relaterade resurser** på menyn väljer du **Länkad arbetsyta**. 
3. Klicka på Ta bort länken till **arbetsytan** om du vill ta bort länken till arbetsytan från ditt Automation-konto.

    ![Ta bort arbetsytans länk](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Flytta arbetsytan

### <a name="azure-portal"></a>Azure Portal
Använd följande procedur för att flytta arbetsytan med Azure-portalen:

1. Öppna menyn **Logganalysarbetsytor** och välj sedan arbetsytan.
2. Klicka på Ändra **bredvid** **resursgrupp** eller **Prenumeration**på sidan **Översikt** .
3. En ny sida öppnas med en lista över resurser som är relaterade till arbetsytan. Välj de resurser som ska flyttas till samma målprenumeration och resursgrupp som arbetsytan. 
4. Välj en **målprenumeration** och **resursgrupp**. Om du flyttar arbetsytan till en annan resursgrupp i samma prenumeration visas inte alternativet **Prenumeration.**
5. Klicka på **OK** om du vill flytta arbetsytan och markerade resurser.

    ![Portalen](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Om du vill flytta arbetsytan med PowerShell använder du [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) som i följande exempel:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Efter flytten ska borttagna lösningar och automationskontolänken konfigureras om för att flytta arbetsytan till sitt tidigare tillstånd.


## <a name="next-steps"></a>Nästa steg
- En lista över vilka resurser som stöder flytta finns i [Flytta åtgärdsstöd för resurser](../../azure-resource-manager/management/move-support-resources.md).
