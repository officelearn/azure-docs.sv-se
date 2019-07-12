---
title: Fråga efter data i Azure Monitor med Azure Data Explorer (förhandsversion)
description: I det här avsnittet att fråga efter data i Azure Monitor genom att skapa en Azure Data Explorer-proxy för kryssprodukten frågor med Application Insights och Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811198"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Monitor med Azure Data Explorer (förhandsversion)

Azure Data Explorer proxy klustret (ADX Proxy) är en entitet som gör att du kan utföra kryssprodukten frågor mellan Azure Data Explorer [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), och [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) i den [Azure Monitor](/azure/azure-monitor/) service. Du kan mappa Azure Monitor Log Analytics-arbetsytor eller Application Insights-appar som ett proxy-kluster. Du kan fråga proxy-kluster med hjälp av Azure Data Explorer-verktyg och referera till det i en fråga mellan kluster. Artikeln visar hur du ansluter till en proxy-kluster, lägga till en proxy-kluster i Azure Data Explorer-Webbgränssnittet och kör frågor mot dina AI-appar eller LA arbetsytor från Azure Data Explorer.

Azure Data Explorer proxy flödet: 

![ADX proxy flöde](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> ADX-Proxy är i förhandsgranskningsläge. Om du vill aktivera funktionen genom att kontakta den [ADXProxy](mailto:adxproxy@microsoft.com) team.

## <a name="connect-to-the-proxy"></a>Ansluta till proxyservern

1. Verifiera ditt interna Datautforskaren i Azure-kluster (till exempel *hjälper* kluster) visas på den vänstra menyn innan du ansluter till din Log Analytics eller Application Insights-kluster.

    ![ADX interna kluster](media/adx-proxy/web-ui-help-cluster.png)

1. I Användargränssnittet för Azure Data Explorer (https://dataexplorer.azure.com/clusters) väljer **Lägg till kluster**.

1. I den **Lägg till kluster** fönster:

    * Lägg till URL: en till LA eller AI-kluster. Exempel: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Välj **Lägg till**.

    ![Lägga till kluster](media/adx-proxy/add-cluster.png)

    Om du lägger till en anslutning till mer än en proxy-kluster, ge var och ett annat namn. Annars har de alla samma namn i den vänstra rutan.

1. När anslutningen har upprättats, visas ditt LA eller AI-kluster i den vänstra rutan med inbyggda ADX klustret. 

    ![Log Analytics och Azure Data Explorer-kluster](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Köra frågor

Du kan använda Kusto Explorer ADX web Explorer Jupyter Kqlmagic eller REST API för att skicka frågor till proxy-kluster. 

> [!TIP]
> * Databasnamnet bör ha samma namn som den resurs som angavs i proxy-klustret. Är skiftlägeskänsliga.
> * I kluster frågor mellan, se till att den [namngivning av appar och arbetsytor](#application-insights-app-and-log-analytics-workspace-names) är korrekt.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Fråga mot det interna Datautforskaren i Azure-klustret 

Köra frågor i Datautforskaren i Azure-kluster (till exempel *StormEvents* tabellen i *hjälpa* kluster). När du kör frågan kan du kontrollera att ditt interna Datautforskaren i Azure-kluster har valts i den vänstra rutan.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents frågetabell](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Skicka frågor mot ditt LA eller AI-kluster

När du kör frågor på LA eller AL klustret kan du kontrollera att din LA eller AI-kluster har valts i den vänstra rutan. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Fråga LA arbetsyta](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Fråga din LA eller AI-kluster från ADX-proxy  

När du kör frågor på dina LA eller AI-kluster från proxy kan du kontrollera ditt interna ADX-kluster har valts i den vänstra rutan. I följande exempel visar en fråga i arbetsytan LA med inbyggda ADX-kluster

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Fråga från Azure Data Explorer-proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Mellan fråga på LA eller AI-kluster och klustret ADX från ADX-proxy 

När du kör frågor mellan kluster från proxy kan du kontrollera ditt interna ADX-kluster har valts i den vänstra rutan. Följande exempel visar att kombinera ADX kluster tabeller (med hjälp av `union`) med LA arbetsyta.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Mellan frågan från Azure Data Explorer-proxy](media/adx-proxy/cross-query-adx-proxy.png)

Med hjälp av den [ `join` operatorn](/azure/kusto/query/joinoperator), i stället för union-, kan kräva ett tips för att köra den på ett kluster som är interna för Azure Data Explorer (och inte på proxy). 

## <a name="additional-syntax-examples"></a>Ytterligare syntaxexempel

Följande Syntaxalternativ är tillgängliga när du anropar Application Insights (AI) eller Log Analytics (LA)-kluster:

|Syntax beskrivning  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databas inom ett kluster som innehåller den definierade resursen i den här prenumerationen (**rekommenderas för mellan kluster frågor**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Kluster som innehåller alla appar/arbetsytor i den här prenumerationen    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Kluster som innehåller alla appar/arbetsytor i prenumerationen och är medlemmar i den här resursgruppen    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Kluster som innehåller den definierade resursen i den här prenumerationen      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights-app och Log Analytics-Arbetsytenamn

* Om namn innehåller specialtecken, är de ersättas med URL-kodning i proxy klustrets namn. 
* Om namn innehåller tecken som inte uppfyller [KQL identifierare regler](/azure/kusto/query/schema-entities/entity-names), är ersättas med ett streck **-** tecken.

## <a name="next-steps"></a>Nästa steg

[Skriv frågor](write-queries.md)