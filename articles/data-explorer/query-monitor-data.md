---
title: Frågedata i Azure Monitor med Azure Data Explorer (förhandsversion)
description: I det här avsnittet frågar du data i Azure Monitor genom att skapa en Azure Data Explorer-proxy för korsproduktfrågor med Application Insights och Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560430"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Frågedata i Azure Monitor med Hjälp av Azure Data Explorer (förhandsversion)

Azure Data Explorer proxykluster (ADX Proxy) är en entitet som gör att du kan utföra korsproduktfrågor mellan Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)och [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) i [Azure Monitor-tjänsten.](/azure/azure-monitor/) Du kan mappa Azure Monitor Log Analytics arbetsytor eller Application Insights-appar som proxykluster. Du kan sedan fråga proxyklustret med hjälp av Azure Data Explorer-verktyg och referera till det i en korsklusterfråga. Artikeln visar hur du ansluter till ett proxykluster, lägger till ett proxykluster i Azure Data Explorer Web UI och kör frågor mot dina AI-appar eller LA-arbetsytor från Azure Data Explorer.

Proxyflödet för Azure Data Explorer: 

![ADX-proxyflöde](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Krav

> [!NOTE]
> ADX-proxyn är i förhandsgranskningsläge. [Anslut till proxyn](#connect-to-the-proxy) för att aktivera ADX-proxyfunktionen för dina kluster. Kontakta [ADXProxy-teamet](mailto:adxproxy@microsoft.com) med eventuella frågor.

## <a name="connect-to-the-proxy"></a>Anslut till proxyn

1. Verifiera ditt inbyggda Azure Data *help* Explorer-kluster (till exempel hjälpkluster) visas på den vänstra menyn innan du ansluter till klustret Logganalys eller Application Insights.

    ![ADX-inbyggt kluster](media/adx-proxy/web-ui-help-cluster.png)

1. I Azure Data Explorer-användargränssnittet (https://dataexplorer.azure.com/clusters)väljer du Lägg till **kluster**.

1. Lägg till URL:en i FÖNSTRET Lägg till kluster i fönstret Lägg till **kluster.** 
    
    * För LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * För AI:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Välj **Lägg till**.

    ![Lägga till kluster](media/adx-proxy/add-cluster.png)

    Om du lägger till en anslutning i mer än ett proxykluster ger du var och en ett annat namn. Annars har de alla samma namn i den vänstra rutan.

1. När anslutningen har upprättats visas LA- eller AI-klustret i den vänstra rutan med ditt inbyggda ADX-kluster. 

    ![Loggar Analytics- och Azure Data Explorer-kluster](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Köra frågor

Du kan köra frågorna med hjälp av klientverktyg som stöder Kusto-frågor, till exempel: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Databasnamnet ska ha samma namn som den resurs som anges i proxyklustret. Namn är skiftlägeskänsliga.
> * I korsklusterfrågor kontrollerar du att namngivning av Application Insights-appar och Log Analytics-arbetsytor är korrekt.
>     * Om namnen innehåller specialtecken ersätts de av URL-kodning i proxyklustrets namn. 
>     * Om namnen innehåller tecken som inte uppfyller [KQL-identifierarens namnregler](/azure/kusto/query/schema-entities/entity-names)ersätts de av strecktecknet. **-**

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Direktfråga från ditt LA- eller AI ADX-proxykluster

Kör frågor på DITT LA- eller AI-kluster. Kontrollera att klustret är markerat i den vänstra rutan. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Fråga LA arbetsyta](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Korsfråga för ditt LA- eller AI ADX-proxykluster och ADX-inbyggda kluster 

När du kör korsklusterfrågor från proxyn kontrollerar du att adx-det inbyggda klustret är markerat i den vänstra rutan. Följande exempel visar att adx-klustertabeller (med hjälp av) `union`kombineras med LA-arbetsytan.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Korsfråga från Azure Data Explorer-proxyn](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Använda [ `join` operatorn](/azure/kusto/query/joinoperator), i stället [`hint`](/azure/kusto/query/joinoperator#join-hints) för union, kan kräva en för att köra den på en Azure Data Explorer inbyggt kluster (och inte på proxy). 

## <a name="additional-syntax-examples"></a>Ytterligare syntaxexempel

Följande syntaxalternativ är tillgängliga när du anropar klustren Application Insights (AI) eller Log Analytics (LA):

|Syntax beskrivning  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databas i ett kluster som bara innehåller den definierade resursen i den här prenumerationen (**rekommenderas för korsklusterfrågor)** |   kluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | kluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Kluster som innehåller alla appar/arbetsytor i den här prenumerationen    |     kluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    kluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Kluster som innehåller alla appar/arbetsytor i prenumerationen och som är medlemmar i den här resursgruppen    |   kluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    kluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Kluster som bara innehåller den definierade resursen i den här prenumerationen      |    kluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  kluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Nästa steg

[Skriva frågor](write-queries.md)
