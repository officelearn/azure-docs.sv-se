---
title: "Utvärdera Service Fabric-program med Log Analytics med hjälp av Azure portal | Microsoft Docs"
description: "Du kan använda Service Fabric-lösning i logganalys med Azure-portalen för att bedöma risken och hälsotillståndet för Service Fabric program, micro-tjänster, noder och kluster."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: nini
ms.openlocfilehash: 8c564c0dcbb2f9be286917b2f4d8a40da5406fae
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Utvärdera Service Fabric-program och micro-tjänster med Azure-portalen

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Service Fabric-symbolen](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Den här artikeln beskriver hur du använder Service Fabric-lösning i Log Analytics för att identifiera och felsöka problem i Service Fabric-klustret.

Service Fabric-lösningen använder Azure-diagnostik data från din Service Fabric virtuella datorer genom att samla in data från Azure BOMULLSTUSS tabellerna. Logganalys läser sedan Service Fabric framework händelser, inklusive **händelser för tillförlitlig tjänst**, **aktören händelser**, **drifthändelser**, och **anpassad ETW-händelser**. Med instrumentpanelen lösning kan du visa anmärkningsvärda problem och relevanta händelser i Service Fabric-miljö.

Du måste ansluta Service Fabric-klustret till logganalys-arbetsytan för att komma igång med lösningen. Här följer tre scenarier att tänka på:

1. Om du inte har distribuerat Service Fabric-kluster använder du stegen i ***distribuera ett Service Fabric-kluster som är ansluten till logganalys-arbetsytan*** att distribuera ett nytt kluster och den är konfigurerad för att rapportera till logganalys.
2. Om du behöver samla in prestandaräknare från värdar att använda andra OMS-lösningar, till exempel säkerhet på Service Fabric-kluster, följer du stegen i ***distribuera ett Service Fabric-kluster som är ansluten till logganalys-arbetsytan med VM-tillägg som är installerad.***
3. Om du redan har distribuerat din Service Fabric-kluster och vill ansluta till logganalys följer du stegen i ***att lägga till ett befintligt lagringskonto till logganalys.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Distribuera ett Service Fabric-kluster som ansluten till logganalys-arbetsytan.
Den här mallen gör följande:

1. Distribuerar ett Azure Service Fabric-kluster som redan är ansluten till logganalys-arbetsytan. Du har möjlighet att skapa en ny arbetsyta när mallen distribueras eller ange namnet på en redan befintlig logganalys-arbetsyta.
2. Lägger till diagnostik lagringskontot logganalys-arbetsytan.
3. Gör det möjligt för Service Fabric-lösningen i logganalys-arbetsytan.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

När du har valt knappen distribuera öppnas Azure-portalen med parametrar för att redigera. Glöm inte att skapa en ny resursgrupp om du ange ett nytt namn för logganalys-arbetsyta:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Acceptera de juridiska villkoren och klicka på **skapa** att starta distributionen. När distributionen är klar visas den nya arbetsytan och kluster skapas och WADServiceFabric * händelse, WADWindowsEventLogs och WADETWEvent tabeller som har lagts till:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Distribuera ett Service Fabric-kluster som ansluten till logganalys-arbetsytan med VM-tillägg som är installerad.

Den här mallen gör följande:

1. Distribuerar ett Azure Service Fabric-kluster som redan är ansluten till logganalys-arbetsytan. Du kan skapa en ny arbetsyta eller använda en befintlig.
2. Lägger till diagnostik storage-konton till logganalys-arbetsytan.
3. Gör det möjligt för Service Fabric-lösningen i logganalys-arbetsytan.
4. Installerar tillägget MMA agent i varje virtuell dator skala i Service Fabric-klustret. Du ska kunna visa prestandamått om noderna med MMA-agenten är installerad.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Ange nödvändiga parametrar stegen ovan och startar en distribution. Igen bör du se den nya arbetsytan, kluster och BOMULLSTUSS tabeller alla skapade:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Visar prestandadata

Visa prestandadata från din noder:


[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Starta logganalys-arbetsytan i Azure Portal.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Gå till inställningar i det vänstra fönstret och välj Data >> Windows prestandaräknare >> ”Lägg till valda prestandaräknare”: ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- Använd följande frågor att detaljerad information om viktiga mått om noderna i loggen sökning:

    a. Jämför den genomsnittliga processoranvändningen mellan alla noder under den senaste timmen för en om du vill se vilka noder har problem och med vilka tidsintervall en nod har en topp:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Visa liknande linjediagram för tillgängligt minne på varje nod med den här frågan:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Använd den här frågan om du vill visa en lista över alla noder, visar exakt medelvärdet för tillgängliga megabyte för varje nod:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Du är i de fall som du vill öka detaljnivån till en viss nod genom att undersöka timvis medelvärde, lägsta, högsta och 75 percentil CPU-användning kan göra detta med hjälp av den här frågan (Ersätt datorn fältet):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Mer information om prestandamått i logganalys på den [Operations Management Suite-blogg](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Lägga till ett befintligt lagringskonto till logganalys

Den här mallen lägger bara till dina befintliga lagringskonton till en ny eller befintlig logganalys-arbetsytan.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Att välja en resursgrupp, Välj om du arbetar med en redan befintlig logganalys-arbetsyta ”Använd befintligt” och Sök efter den resursgrupp som innehåller logganalys-arbetsytan. Skapa en ny en om annars.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

När den här mallen har distribuerats, kommer du att kunna finns det lagringskonto som är ansluten till logganalys-arbetsytan. I den här instansen jag har lagt till ett mer lagringskonto till arbetsytan Exchange jag skapade ovan.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Visa händelser för Service Fabric

När distributioner har slutförts och Service Fabric-lösningen har aktiverats på arbetsytan, Välj den **Service Fabric** panelen i logganalys-portalen för att starta Service Fabric-instrumentpanelen. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumnen visas de översta 10 händelserna efter antal som matchar den kolumnen villkoren för det angivna tidsintervallet. Du kan köra en sökning i logg som innehåller hela listan genom att klicka på **se alla** längst ned rätt i varje kolumn, eller genom att klicka på kolumnrubriken.

| **Service Fabric-händelse** | **Beskrivning** |
| --- | --- |
| Anmärkningsvärda problem |En översikt över problem som till exempel RunAsyncFailures RunAsynCancellations och noden används. |
| Operativa händelser |Viktiga operativa händelser, till exempel distributioner och uppgradering av programmet. |
| Händelser för tillförlitlig tjänst |Viktiga tillförlitlig tjänsthändelser sådana Runasyncinvocations. |
| Aktören händelser |Viktiga aktören händelser som genererats av din micro-tjänster, till exempel undantag som utlöses av en aktörsmetod, aktören aktiveringar och avaktiveringar och så vidare. |
| Programhändelser |Alla anpassade ETW händelser som genererats av dina program. |

![Service Fabric-instrumentpanelen](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric-instrumentpanelen](./media/log-analytics-service-fabric/sf4.png)

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Service Fabric.

| Plattform | Styr Agent | Operations Manager-agent | Azure Storage | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minuter |

> [!NOTE]
> Du kan ändra omfånget för dessa händelser i Service Fabric-lösningen genom att klicka på **Data baserat på senaste 7 dagarna** överst på instrumentpanelen. Du kan också visa händelser som genererats under de senaste sju dagarna, en dag eller sex timmar. Du kan också välja **anpassade** att ange ett anpassat datumintervall.
>
>

## <a name="next-steps"></a>Nästa steg

* Använd [loggen söker i logganalys](log-analytics-log-searches.md) att visa detaljerad information för Service Fabric-händelse.
