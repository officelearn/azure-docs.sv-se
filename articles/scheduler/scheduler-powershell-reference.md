---
title: 'Referens för PowerShell-cmdlet: ar – Azure Scheduler'
description: Läs mer om PowerShell-cmdlets för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b179c50af8b1ffc4313a49da978f178915ec9cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489904"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referens för PowerShell-cmdlets för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda PowerShell-cmdletar för att skapa skript för att skapa och hantera Scheduler-jobb och jobbsamlingar. Den här artikeln innehåller större PowerShell-cmdlets för Azure Scheduler med länkar till deras referensartiklar. Om du vill installera Azure PowerShell för Azure-prenumerationen, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Mer information om [Azure Resource Manager-cmdletar](/powershell/azure/overview), se [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Beskrivning |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Inaktiverar en jobbsamling. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Aktiverar en jobbsamling. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hämtar Scheduler-jobb. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hämtar jobbsamlingar. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Hämtar jobbhistorik. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Skapar en HTTP-jobb. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Skapar en jobbsamling. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Skapar ett jobb för Service Bus-kö. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Skapar ett jobb för Service Bus-ämne. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Skapar ett jobb för Storage-kö. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Tar bort ett Scheduler-jobb. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Tar bort en jobbsamling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Ändrar ett Scheduler-HTTP-jobb. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Ändrar en jobbsamling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Ändrar ett jobb för Service Bus-kö. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Ändrar ett jobb för Service Bus-ämne. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Ändrar ett jobb för Storage-kö. |
||| 

Mer information kan du köra dessa cmdlet: ar: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Skapa och schemalägga ditt första jobb – Azure portal](scheduler-get-started-portal.md)
* [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
