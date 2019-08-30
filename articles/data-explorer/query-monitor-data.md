---
title: Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)
description: I det här avsnittet ska du fråga efter data i Azure Monitor genom att skapa en Azure Datautforskaren proxy för kors produkt frågor med Application Insights och Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172692"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)

Azure Datautforskaren proxy-klustret (ADX proxy) är en entitet som gör att du kan utföra kors produkt frågor mellan Azure Datautforskaren, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)och [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) i [Azure Monitor](/azure/azure-monitor/) -tjänsten. Du kan mappa Azure Monitor Log Analytics arbets ytor eller Application Insights appar som ett proxy-kluster. Du kan sedan fråga proxy-klustret med hjälp av Azure Datautforskaren-verktyg och se det i en kors kluster fråga. Artikeln visar hur du ansluter till ett proxy-kluster, lägger till ett proxy-kluster i Azure Datautforskaren-webbgränssnittet och kör frågor mot dina AI-appar eller LA arbets ytor från Azure Datautforskaren.

Azure Datautforskaren proxy-flödet: 

![ADX-proxy-flöde](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> ADX proxy är i förhands gransknings läge. Om du vill aktivera den här funktionen kontaktar du [ADXProxy](mailto:adxproxy@microsoft.com) -teamet.

## <a name="connect-to-the-proxy"></a>Anslut till proxyn

1. Verifiera att ditt Azure Datautforskaren interna kluster (till exempel *Hjälp* kluster) visas på den vänstra menyn innan du ansluter till Log Analytics eller Application Insights klustret.

    ![Internt ADX-kluster](media/adx-proxy/web-ui-help-cluster.png)

1. I Azure datautforskaren UI (https://dataexplorer.azure.com/clusters) väljer du **Lägg till kluster**.

1. I fönstret **Lägg till kluster** :

    * Lägg till URL: en i LA-eller AI-klustret. Exempel: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Välj **Lägg till**.

    ![Lägga till kluster](media/adx-proxy/add-cluster.png)

    Om du lägger till en anslutning till fler än ett proxy-kluster måste du ange ett annat namn. Annars har de samma namn i det vänstra fönstret.

1. När anslutningen har upprättats visas ditt LA-eller AI-kluster i den vänstra rutan med ditt interna ADX-kluster. 

    ![Log Analytics och Azure Datautforskaren-kluster](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Köra frågor

Du kan använda Kusto Explorer, ADX Web Explorer, Jupyter Kqlmagic eller REST API för att fråga proxy-klustren. 

> [!TIP]
> * Databas namnet måste ha samma namn som den angivna resursen i proxy-klustret. Namn är Skift läges känsliga.
> * I kors kluster frågor kontrollerar du att namngivningen av Application Insights appar och Log Analytics arbets ytor är korrekt.
>     * Om namnen innehåller specialtecken ersätts de av URL-kodningen i namnet på proxyservern. 
>     * Om namnen innehåller tecken som inte uppfyller [KQL-ID: n](/azure/kusto/query/schema-entities/entity-names), ersätts de av bindestrecks **-** tecknet.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Fråga mot det interna Azure Datautforskaren-klustret 

Kör frågor på Azure Datautforskaren-klustret (till exempel *StormEvents* -tabellen i *Hjälp* -kluster). När du kör frågan kontrollerar du att ditt interna Azure Datautforskaren-kluster är markerat i det vänstra fönstret.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Fråga StormEvents-tabell](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Fråga mot ditt LA-eller AI-kluster

När du kör frågor på ditt LA-eller AL-kluster kontrollerar du att ditt LA-eller AI-kluster är markerat i det vänstra fönstret. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Fråga efter LA-arbetsytan](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Fråga ditt LA-eller AI-kluster från ADX-proxyn  

När du kör frågor på ditt LA-eller AI-kluster från proxyservern kontrollerar du att ditt ADX-interna kluster är markerat i det vänstra fönstret. I följande exempel demonstreras en fråga från arbets ytan LA med det inbyggda ADX-klustret

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Fråga från Azure Datautforskaren proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Kors fråga för LA-eller AI-kluster och ADX-kluster från ADX-proxyn 

När du kör kors kluster frågor från proxyservern kontrollerar du att ditt ADX-interna kluster är markerat i det vänstra fönstret. Följande exempel visar hur man kombinerar ADX-kluster tabeller `union`(med) med arbets ytan La.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Kors fråga från Azure Datautforskaren proxy](media/adx-proxy/cross-query-adx-proxy.png)

Genom att [ `join` ](/azure/kusto/query/joinoperator)använda operatorn i stället för union kan du behöva en ledtråd för att köra den på ett internt Azure datautforskaren-kluster (och inte på proxyn). 

## <a name="additional-syntax-examples"></a>Exempel på ytterligare syntax

Följande syntax är tillgängliga när du anropar Application Insights (AI) eller Log Analytics (LA)-kluster:

|Beskrivning av syntax  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databas i ett kluster som bara innehåller den definierade resursen i den här prenumerationen (**rekommenderas för kors kluster frågor**) |   kluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | kluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Kluster som innehåller alla appar/arbets ytor i den här prenumerationen    |     kluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    kluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Kluster som innehåller alla appar/arbets ytor i prenumerationen och som är medlemmar i den här resurs gruppen    |   kluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    kluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Kluster som bara innehåller den definierade resursen i den här prenumerationen      |    kluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  kluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Nästa steg

[Skriv frågor](write-queries.md)