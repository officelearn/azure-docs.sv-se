---
title: Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)
description: I det här avsnittet ska du fråga efter data i Azure Monitor genom att skapa en Azure Datautforskaren proxy för kors produkt frågor med Application Insights och Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560430"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)

Azure Datautforskaren proxy-klustret (ADX proxy) är en entitet som gör att du kan utföra kors produkt frågor mellan Azure Datautforskaren, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)och [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) i [Azure Monitor](/azure/azure-monitor/) -tjänsten. Du kan mappa Azure Monitor Log Analytics arbets ytor eller Application Insights appar som proxy-kluster. Du kan sedan fråga proxy-klustret med hjälp av Azure Datautforskaren-verktyg och se det i en kors kluster fråga. Artikeln visar hur du ansluter till ett proxy-kluster, lägger till ett proxy-kluster i Azure Datautforskaren-webbgränssnittet och kör frågor mot dina AI-appar eller LA arbets ytor från Azure Datautforskaren.

Azure Datautforskaren proxy-flödet: 

![ADX-proxy-flöde](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> ADX proxy är i förhands gransknings läge. [Anslut till proxyn](#connect-to-the-proxy) för att aktivera ADX-proxy-funktionen för dina kluster. Kontakta [ADXProxy](mailto:adxproxy@microsoft.com) -teamet med några frågor.

## <a name="connect-to-the-proxy"></a>Anslut till proxyn

1. Verifiera att ditt Azure Datautforskaren interna kluster (till exempel *Hjälp* kluster) visas på den vänstra menyn innan du ansluter till Log Analytics eller Application Insights klustret.

    ![Internt ADX-kluster](media/adx-proxy/web-ui-help-cluster.png)

1. I Azure Datautforskaren UI (https://dataexplorer.azure.com/clusters)väljer du **Lägg till kluster**.

1. I fönstret **Lägg till kluster** lägger du till URL: en i La-eller AI-klustret. 
    
    * För LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * För AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Välj **Lägg till**.

    ![Lägga till kluster](media/adx-proxy/add-cluster.png)

    Om du lägger till en anslutning till fler än ett proxy-kluster måste du ange ett annat namn. Annars har de samma namn i det vänstra fönstret.

1. När anslutningen har upprättats visas ditt LA-eller AI-kluster i den vänstra rutan med ditt interna ADX-kluster. 

    ![Log Analytics och Azure Datautforskaren-kluster](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Köra frågor

Du kan köra frågorna med hjälp av klient verktyg som stöder Kusto-frågor, till exempel: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Databas namnet måste ha samma namn som den angivna resursen i proxy-klustret. Namn är Skift läges känsliga.
> * I kors kluster frågor kontrollerar du att namngivningen av Application Insights appar och Log Analytics arbets ytor är korrekt.
>     * Om namnen innehåller specialtecken ersätts de av URL-kodningen i namnet på proxyservern. 
>     * Om namnen innehåller tecken som inte uppfyller [KQL-ID: n](/azure/kusto/query/schema-entities/entity-names), ersätts de av streck **-s** tecknet.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Direkt fråga från ditt LA-eller AI ADX-proxy-kluster

Kör frågor på ditt LA-eller AI-kluster. Kontrol lera att klustret är markerat i det vänstra fönstret. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Fråga efter LA-arbetsytan](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Kors fråga för ditt LA-eller AI ADX-proxykontot och det ADX interna klustret 

När du kör kors kluster frågor från proxyservern kontrollerar du att ditt ADX-interna kluster är markerat i det vänstra fönstret. Följande exempel visar hur man kombinerar ADX-kluster tabeller (med `union`) med LA-arbetsytan.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![kors fråga från Azure Datautforskaren proxy](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Om du använder [`join`-operatorn](/azure/kusto/query/joinoperator)i stället för union kan det krävas en [`hint`](/azure/kusto/query/joinoperator#join-hints) för att köra den på ett internt Azure datautforskaren-kluster (och inte på proxyn). 

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
