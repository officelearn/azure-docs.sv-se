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
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991104"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referens för PowerShell-cmdlets för Azure Scheduler

> [!IMPORTANT]
> [Med Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras. Att schemalägga jobb, [prova Azure Logic Apps i stället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Du kan använda PowerShell-cmdletar för att skapa skript för att skapa och hantera Scheduler-jobb och jobbsamlingar. Den här artikeln innehåller högre [PowerShell-cmdletar för Azure Scheduler](/powershell/module/azurerm.scheduler) med länkar till deras referensartiklar. Om du vill installera Azure PowerShell för Azure-prenumerationen, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). Mer information om [Azure Resource Manager-cmdletar](/powershell/azure/overview), se [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Beskrivning |
|--------|-------------|
| [Inaktivera AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Inaktiverar en jobbsamling. |
| [Aktivera AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Aktiverar en jobbsamling. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hämtar Scheduler-jobb. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hämtar jobbsamlingar. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Hämtar jobbhistorik. |
| [Ny AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Skapar en HTTP-jobb. |
| [Ny AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Skapar en jobbsamling. |
| [Ny AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Skapar ett jobb för Service Bus-kö. |
| [Ny AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Skapar ett jobb för Service Bus-ämne. |
| [Ny AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Skapar ett jobb för Storage-kö. |
| [Ta bort AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Tar bort ett Scheduler-jobb. |
| [Ta bort AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Tar bort en jobbsamling. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Ändrar ett Scheduler-HTTP-jobb. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Ändrar en jobbsamling. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Ändrar ett jobb för Service Bus-kö. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Ändrar ett jobb för Service Bus-ämne. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Ändrar ett jobb för Storage-kö. |
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
