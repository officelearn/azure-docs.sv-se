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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045005"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referens för PowerShell-cmdlets för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda PowerShell-cmdletar för att skapa skript för att skapa och hantera Scheduler-jobb och jobbsamlingar. Den här artikeln innehåller högre [PowerShell-cmdletar för Azure Scheduler](/powershell/module/azurerm.scheduler) med länkar till deras referensartiklar. Om du vill installera Azure PowerShell för Azure-prenumerationen, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Mer information om [Azure Resource Manager-cmdletar](/powershell/azure/overview), se [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Beskrivning |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Inaktiverar en jobbsamling. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Aktiverar en jobbsamling. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Hämtar Scheduler-jobb. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Hämtar jobbsamlingar. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Hämtar jobbhistorik. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Skapar en HTTP-jobb. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Skapar en jobbsamling. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Skapar ett jobb för Service Bus-kö. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Skapar ett jobb för Service Bus-ämne. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Skapar ett jobb för Storage-kö. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Tar bort ett Scheduler-jobb. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Tar bort en jobbsamling. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Ändrar ett Scheduler-HTTP-jobb. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Ändrar en jobbsamling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Ändrar ett jobb för Service Bus-kö. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Ändrar ett jobb för Service Bus-ämne. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Ändrar ett jobb för Storage-kö. |
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
