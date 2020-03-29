---
title: PowerShell-cmdlets referens
description: Lär dig mer om PowerShell-cmdletar för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898487"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell-cmdletar referens för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

Om du vill skapa och hantera Scheduler-jobb och jobbsamlingar kan du använda PowerShell-cmdletar. I den här artikeln visas de större PowerShell-cmdletsna för Azure Scheduler med länkar till deras referensartiklar. Information om hur du installerar Azure PowerShell för din Azure-prenumeration finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Mer information om [azure Resource Manager-cmdlets](/powershell/azure/overview)finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Beskrivning |
|--------|-------------|
| [Inaktivera-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Inaktiverar en jobbsamling. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Aktiverar en jobbsamling. |
| [Få-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hämtar Schemaläggarjobb. |
| [Få-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hämtar jobbsamlingar. |
| [Få-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Får jobbhistorik. |
| [Nya-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Skapar ett HTTP-jobb. |
| [Ny-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Skapar en jobbsamling. |
| [Nya-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Skapar ett servicebussköjobb. |
| [Nya-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Skapar ett servicebussämnesjobb. |
| [Ny-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Skapar ett lagringsköjobb. |
| [Ta bort-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Tar bort ett Scheduler-jobb. |
| [Ta bort-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Tar bort en jobbsamling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Ändrar ett HTTP-jobb för Schemaläggaren. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Ändrar en jobbsamling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Ändrar ett servicebussköjobb. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Ändrar ett servicebussämnesjobb. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Ändrar ett lagringsköjobb. |
||| 

För mer information, kan du köra någon av dessa cmdlets: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)