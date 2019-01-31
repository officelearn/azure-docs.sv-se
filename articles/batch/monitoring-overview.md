---
title: Övervaka Azure Batch | Microsoft Docs
description: Läs mer om Azures övervakningstjänster, mått, diagnostikloggar och andra övervakningsfunktioner för Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: a04161d80a6dd3f9db2a95a8556eaac466407047
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470084"
---
# <a name="monitor-batch-solutions"></a>Övervaka Batch-lösningar

Azure och Batch-tjänsten tillhandahåller ett antal tjänster, verktyg och API: er för att övervaka dina Batch-lösningar. Den här översiktsartikeln hjälper dig att välja en övervakning metod som passar dina behov.

En översikt över Azure-komponenter och tjänster som är tillgängliga för att övervaka Azure-resurser finns i [övervakning av Azure-program och resurser](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Prenumerationsnivå övervakning

På prenumerationsnivån, vilket innefattar Batch-konton, den [Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md) samlar in operativa händelsedata i [flera kategorier](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Batch-konton mer specifikt aktivitetsloggen samlar du in händelser som rör hantering av skapandet och borttagningen och nyckel.

Ett sätt att hämta händelser från din aktivitetslogg är att använda Azure-portalen. Klicka på **alla tjänster** > **aktivitetsloggen**. Eller fråga efter händelser med hjälp av Azure CLI, PowerShell-cmdletar och REST-API i Azure Monitor. Du kan också exportera aktivitetsloggen eller konfigurera [aktivitetsloggaviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Övervakning av batch-konto på

Övervaka varje Batch-konto med hjälp av funktionerna i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [mått](../azure-monitor/platform/data-collection.md#metrics) och eventuellt [diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) för resurser som är begränsade på nivån för ett Batch-konto, till exempel pooler, jobb och aktiviteter. Samla in och använda dessa data manuellt eller programmässigt att övervaka aktiviteter i Batch-kontot och diagnostisera problem. Mer information finns i [Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning](batch-diagnostics.md).
 
> [!NOTE]
> Mått är tillgängliga som standard i ditt Batch-konto utan ytterligare konfiguration och de har en rullande 30-dagars historik. Du måste aktivera Diagnostisk loggning för ett Batch-konto och du kan medföra ytterligare kostnader för att lagra eller bearbeta diagnostiklogg data. 

## <a name="batch-resource-monitoring"></a>Övervaka batch-resurser

I dina Batch-program, använder du Batch-API: er att övervaka eller fråga status för dina resurser inklusive jobb, uppgifter, noder och pooler. Exempel:

* [Antal uppgifter och beräkningsnoder efter delstat](batch-get-resource-counts.md)
* [Skapa frågor för att lista Batch-resurser effektivt](batch-efficient-list-queries.md)
* [Skapa aktivitetssamband](batch-task-dependencies.md)
* Använd en [job manager-aktivitet](/rest/api/batchservice/job/add#jobmanagertask)
* Övervaka den [uppgift tillstånd](/rest/api/batchservice/task/list#taskstate)
* Övervaka den [Nodtillstånd](/rest/api/batchservice/computenode/list#computenodestate)
* Övervaka den [lagringspoolen tillstånd](/rest/api/batchservice/pool/get#poolstate)
* Övervaka [lagringspoolen användning i kontot](/rest/api/batchservice/pool/listusagemetrics)
* [Antal poolnoder efter delstat](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Prestandaräknare för virtuell dator och programövervakning

* [Application Insights](../azure-monitor/app/app-insights-overview.md) är en Azure-tjänst du kan använda för att programmässigt övervaka tillgänglighet, prestanda och användning för dina Batch-jobb och uppgifter. Enkelt beräkningsnoder get prestandaräknare från (virtuella datorer) och anpassad information om aktiviteter från de virtuella datorerna. 

  Ett exempel finns i [övervaka och felsöka ett Batch .NET-program med Application Insights](monitor-application-insights.md) och den tillhörande [kodexempel](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Du kan medföra ytterligare kostnader för att använda Application Insights. Se den [prisalternativ](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnadsfritt, omfattande funktioner, fristående klientverktyg för att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Du kan också konfigurera din Batch-lösning till [visa Application Insights-data](https://github.com/Azure/batch-insights) , till exempel prestandaräknare för virtuella datorer i Batch Explorer.


## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Läs mer om [diagnostikloggning](batch-diagnostics.md) med Batch.