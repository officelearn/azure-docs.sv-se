---
title: Övervaka Azure Batch | Microsoft Docs
description: Läs mer om Azure övervakningstjänster, mått, diagnostiska loggar och andra övervakningsfunktioner för Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789954"
---
# <a name="monitor-batch-solutions"></a>Övervaka Batch-lösningar

Azure och Batch-tjänsten tillhandahåller en uppsättning tjänster, verktyg och API: er för att övervaka dina Batch-lösningar. Översikt översiktsartikeln hjälper dig att välja en övervakning metod som passar dina behov.

En översikt över Azure komponenter och tjänster som är tillgängliga för att övervaka Azure-resurser, se [övervakning Azure-program och resurser](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Prenumerationsnivån övervakning

På prenumerationsnivån, vilket innefattar Batch-konton, den [Azure aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) samlar in operativa händelsedata i [flera kategorier](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

För Batch-konton i synnerhet aktivitetsloggen samlar du in händelser relaterade till kontohantering skapandet och borttagningen och nyckel.

Ett sätt att hämta händelser från din aktivitetsloggen är att använda Azure-portalen. Klicka på **alla tjänster** > **aktivitetsloggen**. Eller fråga för händelser med hjälp av Azure CLI eller PowerShell-cmdlets Azure övervakaren REST API. Du kan också exportera aktivitetsloggen eller konfigurera [aktivitet loggen aviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Övervakning av batch-konto på objektnivå

Övervaka varje Batch-kontot med funktionerna i [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure övervakaren samlar in [mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och eventuellt [diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) för resurser omfång på nivån i en Batch-kontot, till exempel pooler, jobb och uppgifter. Samla in och använda dessa data manuellt eller programmässigt att övervaka aktiviteter i Batch-kontot och diagnostisera problem. Mer information finns i [Batch-mätvärden, aviseringar och loggar för övervakning och diagnostik utvärdering](batch-diagnostics.md).
 
> [!NOTE]
> Mått är tillgängliga som standard i Batch-kontot utan ytterligare konfiguration och de har en 30-dagars löpande historik. Du måste aktivera diagnostikloggning för Batch-kontot och du kan innebära ytterligare kostnader för att lagra eller bearbeta diagnostiska loggdata. 

## <a name="batch-resource-monitoring"></a>Övervaka batch-resurser

Använd Batch-API: er att övervaka eller fråga status för dina resurser inklusive jobb, uppgifter, noder och pooler i Batch-program. Exempel:

* [Räkna aktiviteter efter tillstånd](batch-get-task-counts.md)
* [Skapa frågor till listan Batch resurser effektivt](batch-efficient-list-queries.md)
* [Skapa uppgift beroenden](batch-task-dependencies.md)
* Använd en [manager projektaktivitet](/rest/api/batchservice/job/add#jobmanagertask)
* Övervaka den [uppgiften tillstånd](/rest/api/batchservice/task/list#taskstate)
* Övervaka den [nodens tillstånd](/rest/api/batchservice/computenode/list#computenodestate)
* Övervaka den [poolen tillstånd](/rest/api/batchservice/pool/get#poolstate)
* Övervakaren [poolen användning i kontot](/rest/api/batchservice/pool/listusagemetrics)
* [Antal poolen nod efter tillstånd](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Prestandaräknare för VM- och programövervakning

* [Application Insights](../application-insights/app-insights-overview.md) är en Azure-tjänst du kan använda för att övervaka programmässigt tillgänglighet, prestanda och användning av dina batchjobb och uppgifter. Enkelt compute get prestandaräknare från-noder (virtuella datorer) och anpassad information om aktiviteter från de virtuella datorerna. 

  Ett exempel finns [övervaka och felsöka ett Batch-.NET-program med Application Insights](monitor-application-insights.md) och den tillhörande [kodexemplet](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Du kan innebära ytterligare kostnader för att använda Application Insights. Finns det [priser alternativ](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner för att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchLabs/) för Mac, Linux eller Windows. Du kan också konfigurera Batch-lösningen för att [Application Insights-data visas](https://github.com/Azure/batch-insights) , till exempel VM prestandaräknare i BatchLabs.


## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Lär dig mer om [diagnostikloggning](batch-diagnostics.md) med Batch.