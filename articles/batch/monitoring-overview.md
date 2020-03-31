---
title: Övervaka Azure Batch | Microsoft-dokument
description: Lär dig mer om Azure-övervakningstjänster, mått, diagnostikloggar och andra övervakningsfunktioner för Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: d251229c522bd4d6daca894513eaae14d244d8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025868"
---
# <a name="monitor-batch-solutions"></a>Övervaka Batch-lösningar

Azure och batch-tjänsten tillhandahåller en rad tjänster, verktyg och API:er för att övervaka dina batch-lösningar. Den här översiktsartikeln hjälper dig att välja en övervakningsmetod som passar dina behov.

En översikt över de Azure-komponenter och tjänster som är tillgängliga för att övervaka Azure-resurser finns i [Övervaka Azure-program och -resurser](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Övervakning på prenumerationsnivå

På prenumerationsnivå, som inkluderar Batch-konton, samlar [Azure-aktivitetsloggen](../azure-monitor/platform/platform-logs-overview.md) in drifthändelsedata i [flera kategorier](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log).

För Batch-konton specifikt samlar aktivitetsloggen in händelser relaterade till kontoskapande och borttagning och nyckelhantering.

Ett sätt att hämta händelser från din aktivitetslogg är att använda Azure-portalen. Klicka på **Alla tjänster** > **Aktivitetslogg**. Eller fråga efter händelser med Azure CLI, PowerShell-cmdlets eller AZURE Monitor REST API. Du kan också exportera aktivitetsloggen eller konfigurera [aktivitetsloggaviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Övervakning på batchkontonivå

Övervaka varje batchkonto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [mått](../azure-monitor/platform/data-platform-metrics.md) och eventuellt [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md) för resurser som omfattas av ett batchkonto, till exempel pooler, jobb och uppgifter. Samla in och använda dessa data manuellt eller programmässigt för att övervaka aktiviteter i ditt Batch-konto och för att diagnostisera problem. Mer information finns i [Batch-mått, aviseringar och loggar för diagnostikutvärdering och övervakning](batch-diagnostics.md).
 
> [!NOTE]
> Mått är tillgängliga som standard i ditt Batch-konto utan ytterligare konfiguration och de har en rullande 30-dagars historik. Du måste aktivera diagnostikloggning för ett batchkonto och du kan ådra dig ytterligare kostnader för att lagra eller bearbeta diagnostikloggdata. 

## <a name="batch-resource-monitoring"></a>Övervakning av batchresurser

I batch-programmen använder du batch-API:erna för att övervaka eller fråga status för dina resurser, inklusive jobb, aktiviteter, noder och pooler. Ett exempel:

* [Räkna uppgifter och beräkningsnoder efter tillstånd](batch-get-resource-counts.md)
* [Skapa frågor för att lista batchresurser effektivt](batch-efficient-list-queries.md)
* [Skapa aktivitetssamband](batch-task-dependencies.md)
* Använda en [jobbhanterare](/rest/api/batchservice/job/add#jobmanagertask)
* Övervaka [aktivitetstillståndet](/rest/api/batchservice/task/list#taskstate)
* Övervaka [nodtillståndet](/rest/api/batchservice/computenode/list#computenodestate)
* Övervaka [pooltillståndet](/rest/api/batchservice/pool/get#poolstate)
* Övervaka [poolanvändning i kontot](/rest/api/batchservice/pool/listusagemetrics)
* [Räkna poolnoder efter tillstånd](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Prestandaräknare för virtuella datorer och programövervakning

* [Application Insights](../azure-monitor/app/app-insights-overview.md) är en Azure-tjänst som du kan använda för att programmässigt övervaka tillgänglighet, prestanda och användning av dina batchjobb och uppgifter. Hämta enkelt prestandaräknare från beräkningsnoder (VMs) och anpassad information för uppgifter utanför de virtuella datorerna. 

  Ett exempel finns i [Övervaka och felsöka ett Batch .NET-program med Application Insights](monitor-application-insights.md) och det medföljande [kodexemplet](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Du kan ådra dig ytterligare kostnader för att använda Application Insights. Se [prisalternativen](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnadsfritt, utsålt, fristående klientverktyg som hjälper dig att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Konfigurera även batch-lösningen för att [visa Programstatistikdata,](https://github.com/Azure/batch-insights) till exempel prestandaräknare för virtuella datorer i Batch Explorer.


## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Läs mer om [diagnostikloggning](batch-diagnostics.md) med Batch.