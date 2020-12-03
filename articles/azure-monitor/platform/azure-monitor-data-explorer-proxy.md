---
title: Fråga Azure Datautforskaren över resurs med Azure Monitor
description: Använd Azure Monitor för att utföra kors produkt frågor mellan Azure Datautforskaren, Log Analytics arbets ytor och klassiska Application Insights program i Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553885"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Fråga Azure Datautforskaren över resurs med Azure Monitor
Azure Monitor stöder kors tjänst frågor mellan Azure Datautforskaren, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)och [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Du kan sedan fråga ditt Azure Datautforskaren-kluster med Log Analytics/Application Insights-verktyg och se det i en kors tjänst fråga. Artikeln visar hur du skapar en kors tjänst fråga.

Azure Monitor kors tjänst flöde: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure Monitor och Azure-datautforskaren kors tjänst flöde.":::

>[!NOTE]
>* Azure Monitor proxy är i privat förhands granskning-AllowListing krävs.
>* Kontakta [tjänst teamet](mailto:ADXProxy@microsoft.com) om du har frågor.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Kors fråga Log Analytics-eller Application Insights resurser och Azure Datautforskaren

Du kan köra kors resurs frågorna med hjälp av klient verktyg som har stöd för Kusto-frågor, till exempel: Log Analytics webb gränssnitt, arbets böcker, PowerShell, REST API med mera.

* Ange identifieraren för ett Azure Datautforskaren-kluster i en fråga i mönstret "ADX" följt av databasens namn och tabell.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Exempel på kors tjänst fråga.":::

> [!NOTE]
>* Databas namn är Skift läges känsliga.
>* Kors resurs frågan som en avisering stöds inte.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Kombinera Azure Datautforskaren Cluster-tabeller (med union och Join) med arbets ytan LA.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Exempel på kors tjänst fråga med Union.":::

>[!Tip]
>* Kort format är tillåtet-kluster-/InitialCatalog. Till exempel ADX ("hjälp/Samples") översätts till ADX ("Help. kusto. Windows. net/Samples")
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Koppla data från ett Azure Datautforskaren-kluster i en klient organisation med en Azure Monitor-resurs i en annan

Frågor över flera klienter mellan-tjänsterna stöds inte. Du är inloggad på en enda klient för att köra frågan som sträcker sig över båda resurserna.

Om Azure Datautforskaren-resursen finns i klient organisationen "A" och Log Analytics arbets ytan är i klient organisationen "B" använder du någon av följande två metoder:

*  Med Azure Datautforskaren kan du lägga till roller för huvud konton i olika klienter. Lägg till ditt användar-ID i klienten ' B ' som en behörig användare i Azure Datautforskaren-klustret. Verifiera att egenskapen *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* i Azure datautforskaren-klustret innehåller klienten ' B '. Kör kors frågan fullständigt i klient organisationen ' B '.
*  Använd [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) för att projicera Azure Monitor resursen i klient organisationen "A".
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ansluta till Azure Datautforskaren-kluster från olika klienter

Kusto Explorer loggar automatiskt in till den klient som användar kontot ursprungligen tillhör. För att få åtkomst till resurser i andra klienter med samma användar konto måste det `tenantId` uttryckligen anges i anslutnings strängen: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Nästa steg
* [Skriv frågor](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Fråga efter data i Azure Monitor med Azure Datautforskaren](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Utföra kors resurs logg frågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






