---
title: Utvärdera Service Fabric-program med Log Analytics med hjälp av Azure-portalen | Microsoft Docs
description: Du kan använda Service Fabric-lösningen i Log Analytics med Azure portal för att bedöma risken och hälsotillståndet för dina Service Fabric-program, mikrotjänster, noder och kluster.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432899"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Utvärdera Service Fabric-program och micro-tjänster med Azure portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Service Fabric-symbol](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Den här artikeln beskriver hur du använder Service Fabric-lösningen i Log Analytics för att identifiera och Felsök problem i Service Fabric-klustret.

Service Fabric-lösningen använder Azure Diagnostics-data från dina Service Fabric virtuella datorer genom att samla in dessa data från dina Azure WAD tabeller. Log Analytics läser sedan Service Fabric framework händelser, inklusive **Reliable Service-händelser**, **aktören händelser**, **drifthändelser**, och **anpassad ETW-händelser**. Med instrumentpanelen för lösningen kan du visa anmärkningsvärda problem och relevanta händelser i din miljö för Service Fabric.

Om du vill komma igång med lösningen, som du behöver ansluta ditt Service Fabric-kluster till en Log Analytics-arbetsyta. Här följer tre scenarier att tänka på:

1. Om du inte har distribuerat Service Fabric-klustret använder du stegen i ***distribuera ett Service Fabric-kluster som är anslutna till en Log Analytics-arbetsyta*** att distribuera ett nytt kluster och har konfigurerats att rapportera till Log Analytics.
1. Om du behöver samla in prestandaräknare från värdar att använda andra lösningar som säkerhet på Service Fabric-kluster, följer du stegen i ***distribuera ett Service Fabric-kluster som är anslutna till en Log Analytics-arbetsyta med VM-tillägg installerad.***
1. Om du redan har distribuerat ditt Service Fabric-kluster och vill ansluta till Log Analytics, följer du stegen i ***att lägga till ett befintligt lagringskonto i Log Analytics.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Distribuera ett Service Fabric-kluster som är anslutna till en Log Analytics-arbetsyta.
Den här mallen gör följande:

1. Distribuerar ett Azure Service Fabric-kluster som redan är anslutna till en Log Analytics-arbetsyta. Du har möjlighet att skapa en ny arbetsyta när du distribuerar mallen, eller ange namnet på en redan befintlig Log Analytics-arbetsyta.
1. Lägger till diagnostik storage-kontot till Log Analytics-arbetsytan.
1. Gör att Service Fabric-lösning i Log Analytics-arbetsytan.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

När du har valt knappen distribuera öppnas Azure-portalen med parametrar som du kan redigera. Tänk på att skapa en ny resursgrupp om du anger ett nytt Log Analytics-Arbetsytenamn:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Godkänn de juridiska villkoren och klicka på **skapa** att starta distributionen. När distributionen är klar visas den nya arbetsytan och -kluster som skapas och WADServiceFabric * händelse, WADWindowsEventLogs och WADETWEvent tabeller som har lagts till:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Distribuera ett Service Fabric-kluster som är anslutna till en Log Analytics-arbetsyta med VM-tillägg installeras.

Den här mallen gör följande:

1. Distribuerar ett Azure Service Fabric-kluster som redan är anslutna till en Log Analytics-arbetsyta. Du kan skapa en ny arbetsyta eller välj en befintlig.
1. Lägger till diagnostik storage-konton till Log Analytics-arbetsytan.
1. Gör att Service Fabric-lösning i Log Analytics-arbetsytan.
1. Installerar MMA-agent-tillägget i varje VM-skalningsuppsättning i Service Fabric-klustret. Med MMA-agenten installerad, kan du visa prestandamått om noderna.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Ange de nödvändiga parametrarna efter samma stegen ovan, och sätta igång en distribution. Återigen bör du se den nya arbetsytan, kluster och WAD tabeller alla skapade:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Visa prestandadata

Visa prestandadata från noderna:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Starta Log Analytics-arbetsytan från Azure-portalen.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Gå till inställningarna på den vänstra rutan och välj Data >> Windows prestandaräknare >> ”Lägg till valda prestandaräknare”: ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- I Loggsökning, använder du följande frågor för att gräver viktiga mått om dina noder:

    a. Jämför den genomsnittliga CPU-användningen över alla noder i den senaste timmen för att se vilka noder som har problem med och med vilken tidsintervall en nod har en topp:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Visa liknande linjediagram för tillgängligt minne på varje nod med den här frågan:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Använd den här frågan om du vill visa en lista över alla noder, som visar exakt medelvärdet för tillgängliga megabyte för varje nod:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. Om du vill granska nedåt i en viss nod genom att undersöka per timme average, lägsta, högsta och 75: e percentilen CPU-användning, är du kan göra detta med hjälp av den här frågan (Ersätt Computer i fältet):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Mer information om prestandamått i Log Analytics på den [Operations Management Suite-bloggen](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Att lägga till ett befintligt lagringskonto i Log Analytics

Den här mallen lägger bara till dina befintliga lagringskonton till en ny eller befintlig Log Analytics-arbetsyta.

[![Distribuera till Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Väljer en resursgrupp, Välj om du arbetar med en redan befintlig Log Analytics-arbetsyta ”Använd befintligt” och Sök efter resursgruppen som innehåller Log Analytics-arbetsytan. Skapa en ny en om annars.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

När den här mallen har distribuerats, kommer du att kunna se storage-konto som är ansluten till Log Analytics-arbetsytan. I den här instansen, jag har lagt till ett mer lagringskonto till Exchange-arbetsyta som jag skapade ovan.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Visa händelser för Service Fabric

När distributionen har slutförts och Service Fabric-lösningen har aktiverats i din arbetsyta, väljer den **Service Fabric** panelen i Log Analytics-portalen för att starta Service Fabric-instrumentpanelen. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar de översta 10 händelserna med antalet som matchar denna kolumns kriterier för det angivna tidsintervallet. Du kan köra en loggsökning som innehåller hela listan genom att klicka på **se alla** längst ned i varje kolumn eller genom att klicka på kolumnrubriken.

| **Service Fabric-händelse** | **Beskrivning** |
| --- | --- |
| Anmärkningsvärda problem |En översikt över problem som till exempel RunAsyncFailures RunAsynCancellations och noden listrutor. |
| Drifthändelser |Viktiga operativa händelser, till exempel uppgradera programmet och distributioner. |
| Reliable Services-händelser |Viktiga tillförlitlig tjänsthändelser sådana Runasyncinvocations. |
| Aktören händelser |Viktig aktör händelser som genererats av din micro-tjänster, till exempel undantag som utlöses av en aktörsmetod, aktören aktiveringar och avaktiveringar och så vidare. |
| Programhändelser |Alla anpassade ETW-händelser genereras av dina program. |

![Service Fabric-instrumentpanel](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric-instrumentpanel](./media/log-analytics-service-fabric/sf4.png)

I följande tabell visas data samlingsmetoder och annan information om hur data samlas in för Service Fabric.

| Plattform | Direktagent | Operations Manager-agent | Azure Storage | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minuter |

> [!NOTE]
> Du kan ändra omfånget för de här händelserna i Service Fabric-lösningen genom att klicka på **Data baserat på senaste 7 dagarna** överst på instrumentpanelen. Du kan också visa händelser som genererats under de senaste sju dagarna, en dag eller sex timmar. Du kan också välja **anpassade** att ange ett eget datumintervall.
>
>

## <a name="next-steps"></a>Nästa steg

* Använd [Loggsökningar i Log Analytics](log-analytics-log-searches.md) att visa detaljerade data om Service Fabric.
