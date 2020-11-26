---
title: Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)
description: Använd Azure Datautforskaren för att utföra kors produkt frågor mellan Azure Datautforskaren, Log Analytics arbets ytor och klassiska Application Insights program i Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2a21d7a06e8a92022b620704d1fb51a303da3ae0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185988"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)
Med Azure Datautforskaren proxy-klustret kan du utföra kors produkt frågor mellan Azure Datautforskaren, Log Analytics arbets ytor och klassiska Application Insights program i Azure Monitor. Du kan mappa Log Analytics arbets ytor i Azure Monitor eller klassiska Application Insights appar som proxy-kluster. Du kan sedan fråga proxy-klustret med hjälp av Azure Datautforskaren-verktyg och se det i en kors kluster fråga. Artikeln visar hur du ansluter till ett proxy-kluster, lägger till ett proxy-kluster i Azure Datautforskaren Web UI och kör frågor mot dina Log Analytics-arbetsytor eller klassiska Application Insights-appar från Azure Datautforskaren.

Följande diagram visar Azure Datautforskaren proxy-flödet:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Proxy-flöde för Azure Data Explorer.":::


> [!NOTE]
> Azure Datautforskaren proxy finns i en offentlig för hands version. [Anslut till proxyn](#connect-to-the-proxy) för att aktivera proxy-funktionen för dina kluster. 

## <a name="connect-to-the-proxy"></a>Anslut till proxyn
Om du vill ansluta din Log Analytics arbets yta eller klassisk Application Insights-App öppnar du[Azure datautforskaren-WEBBgränssnittet](https://dataexplorer.azure.com/clusters). Verifiera att ditt Azure Datautforskaren interna kluster (till exempel *Hjälp* kluster) visas på den vänstra menyn innan du ansluter till Log Analytics eller Application Insights klustret.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Datautforskaren internt kluster.":::

Klicka på **Lägg till kluster** och Lägg sedan till URL: en för Log Analytics eller Application Insights klustret i något av följande format. 
    
* För Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* För Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Klicka på **Lägg till** för att ansluta.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Lägg till kluster.":::
 
> [!NOTE]
> Om du lägger till en anslutning till fler än ett proxy-kluster måste du ange ett annat namn. Annars har de samma namn i det vänstra fönstret.

När anslutningen har upprättats visas Log Analytics eller Application Insights klustret i det vänstra fönstret med ditt interna Azure Datautforskaren-kluster. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics och Azure Datautforskaren-kluster.":::
 
> [!NOTE]
> Antalet Azure Monitor arbets ytor som kan mappas är begränsat till 100.

## <a name="create-queries-using-azure-monitor-data"></a>Skapa frågor med hjälp av Azure Monitor data

Du kan köra frågorna med hjälp av klient verktyg som har stöd för Kusto-frågor, till exempel: Kusto Explorer, Azure Datautforskaren Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, lins och REST API.

> [!NOTE]
> Azure Datautforskaren proxy-funktionen används endast för data hämtning. Mer information finns i [funktions stöd](#function-supportability).

> [!TIP]
> * Databas namnet måste ha samma namn som den angivna resursen i proxy-klustret. Namnen är skiftlägeskänsliga.
> * I kors kluster frågor kontrollerar du att namngivningen av Application Insights appar och Log Analytics arbets ytor är korrekt.
>     * Om namnen innehåller specialtecken ersätts de av URL-kodningen i namnet på proxyservern. 
>     * Om namnen innehåller tecken som inte uppfyller [KQL-ID: n](/azure/data-explorer/kusto/query/schema-entities/entity-names), ersätts de av bindestrecks **-** tecknet.

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Direkt fråga från Log Analytics eller Application Insights proxy-kluster

Kör frågor på Log Analytics eller Application Insights klustret. Kontrol lera att klustret är markerat i det vänstra fönstret. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Fråga Log Analytics-arbetsyta.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Kors fråga för ditt Log Analytics-eller Application Insights-proxy-kluster och Azure Datautforskaren internt kluster

När du kör kors kluster frågor från proxyservern kontrollerar du att ditt Azure Datautforskaren inbyggda kluster är markerat i den vänstra rutan. Följande exempel visar hur du kombinerar Azure Datautforskaren Cluster-tabeller med operatorn [union](/azure/data-explorer/kusto/query/unionoperator) med en Log Analytics-arbetsyta.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Genom att använda [ `join` operatorn](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)i stället för union kan du behöva en [ledtråd](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) för att köra den på ett internt Azure datautforskaren-kluster (och inte på proxyn). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Koppla data från ett Azure Datautforskaren-kluster i en klient organisation med en Azure Monitor-resurs i en annan

Frågor över flera klienter stöds inte av Azure Datautforskaren proxy. Du är inloggad på en enda klient för att köra frågan som sträcker sig över båda resurserna.

Om Azure Datautforskaren-resursen finns i klient organisationen "A" och Log Analytics arbets ytan är i klient organisationen "B" använder du någon av följande två metoder:

- Med Azure Datautforskaren kan du lägga till roller för huvud konton i olika klienter. Lägg till ditt användar-ID i klienten ' B ' som en behörig användare i Azure Datautforskaren-klustret. Verifiera att egenskapen *[' TrustedExternalTenant '](/powershell/module/az.kusto/update-azkustocluster)* i Azure datautforskaren-klustret innehåller klienten ' B '. Kör kors frågan fullständigt i klient organisationen ' B '.

- Använd [Lighthouse](../../lighthouse/index.yml) för att projicera Azure Monitor resursen i klient organisationen "A".

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ansluta till Azure Datautforskaren-kluster från olika klienter

Kusto Explorer loggar automatiskt in till den klient som användar kontot ursprungligen tillhör. För att få åtkomst till resurser i andra klienter med samma användar konto måste det `tenantId` uttryckligen anges i anslutnings strängen: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Funktions stöd

Azure Datautforskaren proxy-klustret stöder funktioner för både Log Analytics och Application Insights. Den här funktionen gör det möjligt för kors kluster frågor att referera till en Azure Monitor tabell funktion direkt.

Följande kommandon stöds av proxyn:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Följande bild visar ett exempel på att fråga en tabell funktion från Azure Datautforskaren Web UI. Om du vill använda funktionen kör du namnet i frågefönstret.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Fråga en tabell funktion från Azure Datautforskaren Web UI.":::
 
> [!NOTE]
> Azure Monitor stöder endast tabell funktioner, som inte stöder parametrar.

## <a name="additional-syntax-examples"></a>Exempel på ytterligare syntax

Följande syntax är tillgängliga när du anropar Log Analytics-eller Application Insights-kluster:

|Beskrivning av syntax  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databas i ett kluster som bara innehåller den definierade resursen i den här prenumerationen (**rekommenderas för kors kluster frågor**) |   kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Kluster som innehåller alla appar/arbets ytor i den här prenumerationen    |     kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Kluster som innehåller alla appar/arbets ytor i prenumerationen och som är medlemmar i den här resurs gruppen    |   kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Kluster som bara innehåller den definierade resursen i den här prenumerationen      |    kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [data strukturen för Log Analytics arbets ytor och Application Insights](data-platform-logs.md).
- Lär dig att [skriva frågor i Azure datautforskaren](/azure/data-explorer/write-queries).