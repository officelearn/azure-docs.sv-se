---
title: Övervaka åtgärder, händelser och prestandaräknare för offentlig grundläggande belastningsutjämnare
titlesuffix: Azure Load Balancer
description: Lär dig hur du aktiverar händelser och avsökning hälsotillstånd status loggning för offentlig grundläggande belastningsutjämnare
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: d6b5a8b31b78e21868a7f81c481c4fce1cd4e6f5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192453"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor-loggar för offentlig grundläggande belastningsutjämnare

>[!IMPORTANT] 
>Azure Load Balancer stöder två typer: Basic och Standard. Den här artikeln beskriver den grundläggande lastbalanseraren. Mer information om Standardbelastningsutjämnaren finns i [översikt över Standard Load Balancer](load-balancer-standard-overview.md) som visar telemetri via flerdimensionella mått i Azure Monitor.

Du kan använda olika typer av loggar i Azure för att hantera och felsöka grundläggande belastningsutjämnare. Några av de här loggarna kan nås via portalen. Alla loggar kan extraheras från Azure blob storage och visas i olika verktyg som Excel och Power BI. Du kan lära dig mer om de olika typerna av loggar i listan nedan.

* **Granskningsloggar:** Du kan använda [Azure-granskningsloggarna](../monitoring-and-diagnostics/insights-debugging-with-events.md) (kallades tidigare Driftloggar) att visa alla åtgärder som skickas till dina Azure-prenumerationer och deras status. Granskningsloggarna är aktiverade som standard och kan ses i Azure-portalen.
* **Händelseloggar för avisering:** Du kan använda den här loggfilen för att visa aviseringar som genererats av belastningsutjämnaren. Status för belastningsutjämnaren som samlas in var femte minut. Den här loggfilen skrivs endast om en load balancer varning avisering har genererats.
* **Hälsoavsökningsloggar:** Du kan använda den här loggfilen för att visa problem som identifieras av din hälsoavsökning, till exempel hur många instanser i din serverdelspool som inte tar emot begäranden från belastningsutjämnaren på grund av hälsokontrollfel. Den här loggfilen skrivs till när det finns en ändring i avsökningen hälsostatus.

> [!IMPORTANT]
> Azure Monitor loggar för närvarande fungerar endast för offentlig grundläggande belastningsutjämnare. Loggar är bara tillgängliga för resurser som distribueras i Resource Manager-distributionsmodellen. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. Mer information om distributionsmodellerna finns i [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Aktivera loggning

Granskningsloggning aktiveras automatiskt för alla Resource Manager-resurser. Du måste aktivera händelse och hälsotillstånd avsökningen loggning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning.

Logga in på den [Azure-portalen](http://portal.azure.com). Om du inte redan har en belastningsutjämnare [skapar en belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md) innan du fortsätter.

1. I portalen klickar du på **Bläddra**.
2. Välj **belastningsutjämnare**.

    ![Portal - belastningsutjämnare](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Välj en befintlig belastningsutjämnare >> **alla inställningar**.
4. Bläddra till höger på dialogrutan under namnet på belastningsutjämnaren **övervakning**, klickar du på **diagnostik**.

    ![Portal –--inställningar för belastningsutjämnare](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. I den **diagnostik** fönstret under **Status**väljer **på**.
6. Klicka på **Lagringskonto**.
7. Under **loggar**, Välj ett befintligt lagringskonto eller skapa en ny. Använd skjutreglaget för att avgöra hur många dagar med händelsedata kommer att lagras i händelseloggarna. 
8. Klicka på **Spara**.

Diagnostik kommer att sparas i Table Storage i det angivna lagringskontot. Om loggar inte sparas, beror det på att inga relevanta loggar produceras.

![Portal - diagnostikloggar](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Granskningsloggar kräver inte ett separat lagringskonto. Användningen för händelse- och hälsotillstånd avsökningen loggning tillkommer kostnader för tjänsten.

## <a name="audit-log"></a>Granskningslogg

Granskningsloggen skapas som standard. Loggarna bevaras i 90 dagar i Azures händelseloggar store. Läs mer om de här loggarna genom att läsa den [visar händelser och granskningsloggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) artikeln.

## <a name="alert-event-log"></a>Aviseringen händelseloggen

Den här loggen genereras bara om du har aktiverat det på en per load balancer basis. Händelser loggas i JSON-format och lagras i lagringskontot som du angav när du har aktiverat loggning. Följande är ett exempel på en händelse.

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

JSON-utdata visar den *eventname* egenskap som beskriver orsaken till belastningsutjämnaren skapas en avisering. I det här fallet har den avisering som genererats på grund av TCP-portöverbelastning som orsakats av källan IP NAT gränser (SNAT).

## <a name="health-probe-log"></a>Hälsotillstånd avsökningen log

Den här loggen genereras bara om du har aktiverat det på en per load balancer grund som beskrivs ovan. Data lagras i lagringskontot som du angav när du har aktiverat loggning. En behållare med namnet ”insights-logs-loadbalancerprobehealthstatus' har skapats och följande data loggas:

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

JSON-utdata som visas i egenskapsfältet grundläggande information för avsökningen hälsostatus. Den *dipDownCount* egenskapen visar det totala antalet instanser på backend-server som inte tar emot nätverkstrafik på grund av misslyckade avsökningssvar.

## <a name="view-and-analyze-the-audit-log"></a>Visa och analysera granskningsloggen

Du kan visa och analysera granskningsdata med hjälp av någon av följande metoder:

* **Azure-verktyg:** Hämta information från granskningsloggar via Azure PowerShell, Azure-kommandoradsgränssnitt (CLI), Azure REST-API eller Azure preview portal. Stegvisa instruktioner för varje metod finns beskrivna i den [granskningsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md) artikeln.
* **Power BI:** Om du inte redan har en [Power BI](https://powerbi.microsoft.com/pricing) konto, du kan prova kostnadsfritt. Med hjälp av den [Azure-granskningsloggarna innehållspaket för Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), du kan analysera dina data med förkonfigurerade instrumentpaneler och du kan anpassa vyer så att den passar dina behov.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visa och analysera hälsoavsökning och händelseloggen

Du måste ansluta till ditt storage-konto och hämta JSON-loggposter för händelse- och hälsotillstånd avsökningen loggar. När du har hämtat JSON-filerna kan konvertera du dem till CSV och view i Excel, PowerBI eller andra datavisualiseringsverktyg.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="additional-resources"></a>Ytterligare resurser

* [Visualisera dina Azure-granskningsloggar med Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogginlägg.
* [Visa och analysera Azure-granskningsloggar i Power BI med mera](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

## <a name="next-steps"></a>Nästa steg

[Förstå avsökningar av lastbalanserare](load-balancer-custom-probe-overview.md)
