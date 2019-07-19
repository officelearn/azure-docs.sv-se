---
title: Övervaka åtgärder, händelser och räknare för offentliga Basic-Load Balancer
titlesuffix: Azure Load Balancer
description: Lär dig hur du aktiverar aviserings händelser och loggning av avsöknings hälso status för offentliga Basic-Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274804"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor loggar för offentliga grundläggande Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer stöder två olika typer: Basic och Standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om Standard Load Balancer finns i [standard Load Balancer översikt](load-balancer-standard-overview.md) som visar telemetri via flerdimensionella mått i Azure Monitor.

Du kan använda olika typer av loggar i Azure för att hantera och felsöka grundläggande belastnings utjämning. Vissa av dessa loggar kan nås via portalen. Alla loggar kan extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och PowerBI. Du kan lära dig mer om de olika typerna av loggar i listan nedan.

* **Gransknings loggar:** Du kan använda [Azures gransknings loggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) (tidigare kallade drift loggar) om du vill visa alla åtgärder som skickas till din Azure-prenumeration (er) och deras status. Gransknings loggar är aktiverade som standard och kan visas i Azure Portal.
* **Aviserings händelse loggar:** Du kan använda den här loggen för att visa aviseringar som aktive ras av belastningsutjämnaren. Status för belastningsutjämnaren samlas in var femte minut. Den här loggen skrivs bara om en belastnings Utjämnings aviserings händelse höjs.
* **Hälso avsöknings loggar:** Du kan använda den här loggen för att visa problem som upptäckts av din hälso avsökning, till exempel antalet instanser i din backend-pool som inte tar emot begär Anden från belastningsutjämnaren på grund av hälso avsöknings fel. Loggen skrivs till när statusen för hälso avsökningen ändras.

> [!IMPORTANT]
> Azure Monitor loggar för närvarande endast fungerar för offentliga Basic Load Balancer. Loggar är bara tillgängliga för resurser som distribueras i distributions modellen för Resource Manager. Du kan inte använda loggar för resurser i den klassiska distributions modellen. Mer information om distributions modellerna finns i [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Aktivera loggning

Gransknings loggning aktive ras automatiskt för varje Resource Manager-resurs. Du måste aktivera loggning av händelse-och hälso avsökning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning.

Logga in på [Azure Portal](https://portal.azure.com). Om du inte redan har en belastningsutjämnare måste du [skapa en belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md) innan du fortsätter.

1. I portalen klickar du på **Bläddra**.
2. Välj **belastnings utjämning**.

    ![Portal – belastnings utjämning](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Välj en befintlig belastnings Utjämnings > > **alla inställningar**.
4. Gå till den högra sidan i dialog rutan under namnet på belastningsutjämnaren, bläddra till **övervakning**och klicka på **diagnostik**.

    ![Portal – belastnings utjämning – inställningar](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. I fönstret **diagnostik** under **status**väljer du **på**.
6. Klicka på **lagrings konto**.
7. Under **loggar**väljer du ett befintligt lagrings konto eller skapar ett nytt. Använd skjutreglaget för att avgöra hur många dagars händelse data som ska lagras i händelse loggarna. 
8. Klicka på **Spara**.

Diagnostiken kommer att sparas i Table Storage i det angivna lagrings kontot. Om loggarna inte sparas beror det på att inga relevanta loggar skapas.

![Portal – diagnostikloggar](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Gransknings loggar kräver inget separat lagrings konto. Användningen av lagring för loggning av händelse-och hälso avsökning debiteras service avgifter.

## <a name="audit-log"></a>Gransknings logg

Gransknings loggen skapas som standard. Loggarna bevaras för 90 dagar i Azures händelse logg arkiv. Läs mer om dessa loggar genom att läsa artikeln [Visa händelser och gransknings loggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Aviserings händelse logg

Den här loggen skapas endast om du har aktiverat den per belastningsutjämnare. Händelserna är inloggade i JSON-format och lagras i det lagrings konto du angav när du aktiverade loggningen. Följande är ett exempel på en händelse.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON-utdata visar egenskapen *EventName* som beskriver orsaken till att belastningsutjämnaren skapade en avisering. I det här fallet orsakade den genererade aviseringen på grund av att TCP-portens överbelastning orsakade av NAT-gränser (käll-IP NAT).

## <a name="health-probe-log"></a>Hälso avsöknings logg

Den här loggen skapas endast om du har aktiverat den per belastningsutjämnare enligt beskrivningen ovan. Data lagras i det lagrings konto du angav när du aktiverade loggningen. En behållare med namnet Insights-logs-loadbalancerprobehealthstatus skapas och följande data loggas:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON-utdata visas i fältet egenskaper grundläggande information om avsökningens hälso status. Egenskapen *dipDownCount* visar det totala antalet instanser på backend-sidan som inte tar emot nätverks trafik på grund av misslyckade avsöknings svar.

## <a name="view-and-analyze-the-audit-log"></a>Visa och analysera gransknings loggen

Du kan visa och analysera Gransknings logg data med någon av följande metoder:

* **Azure-verktyg:** Hämta information från gransknings loggarna via Azure PowerShell, Azure Command Line Interface (CLI), Azure-REST API eller Azure Preview Portal. Stegvisa instruktioner för varje metod beskrivs i artikeln [Granska åtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing) konto kan du prova det kostnads fritt. Med [innehålls paketet för Azure audit-loggar för Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)kan du analysera dina data med förkonfigurerade instrument paneler, eller så kan du anpassa vyer så att de passar dina behov.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visa och analysera hälso avsökningen och händelse loggen

Du måste ansluta till ditt lagrings konto och hämta JSON-logg poster för händelse-och hälso avsöknings loggar. När du har hämtat JSON-filerna kan du konvertera dem till CSV och Visa i Excel, PowerBI eller något annat verktyg för data visualisering.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="additional-resources"></a>Ytterligare resurser

* [Visualisera dina Azure audit-loggar med Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogg inlägg.
* [Visa och analysera Azure audit-loggar i Power BI och fler](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogg inlägg.

## <a name="next-steps"></a>Nästa steg

[Förstå avsökningar av lastbalanserare](load-balancer-custom-probe-overview.md)
