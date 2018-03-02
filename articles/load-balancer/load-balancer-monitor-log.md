---
title: "Övervaka åtgärder, händelser och prestandaräknare för belastningsutjämnaren | Microsoft Docs"
description: "Lär dig hur du aktiverar aviseringen händelser och avsökning hälsa status loggning för Azure belastningsutjämnare"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4388a1e933a0ebf211b5a7621c74b0622be41a4c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="log-analytics-for-azure-load-balancer"></a>Logganalys för Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare. Vissa av dessa loggar kan nås via portalen. Alla loggar kan extraheras från Azure blob storage och visas i olika verktyg som Excel och PowerBI. Mer information om de olika typerna av loggar i listan nedan.

* **Granskningsloggar:** du kan använda [Azure-granskningsloggarna](../monitoring-and-diagnostics/insights-debugging-with-events.md) (kallades tidigare operativa loggar) att visa alla åtgärder som skickas till din Azure-prenumeration(er) och deras status. Granskningsloggar är aktiverade som standard och kan visas i Azure-portalen.
* **Varna händelseloggar:** du kan använda den här loggen för att visa aviseringar som genererats av belastningsutjämnaren. Status för belastningsutjämnaren samlas var femte minut. Den här loggen skrivs endast om en belastningsutjämnare avisering händelsen utlöses.
* **Hälsotillstånd avsökningen loggar:** du kan använda den här loggen för att visa problem som identifieras av din hälsoavsökningen, till exempel antal instanser i en serverdelspool som inte tar emot förfrågningar från belastningsutjämnaren på grund av fel för avsökning av hälsotillstånd. Den här loggen skapas i när det finns en ändring i avsökningen hälsostatus.

> [!IMPORTANT]
> Logga analytics fungerar för närvarande endast för belastningsutjämnare mot Internet. Loggarna är bara tillgängliga för resurser som är distribuerad i Resource Manager-distributionsmodellen. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. Mer information om distributionsmodellerna finns [förstå Resource Manager distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Aktivera loggning

Granskningsloggning aktiveras automatiskt för varje resurs för hanteraren för filserverresurser. Du måste aktivera händelse och hälsotillstånd avsökningen loggning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning.

Logga in på den [Azure-portalen](http://portal.azure.com). Om du inte redan har en belastningsutjämnare [skapa en belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md) innan du fortsätter.

1. I portalen klickar du på **Bläddra**.
2. Välj **belastningsutjämnare**.

    ![Portal - belastningsutjämnare](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Välj en befintlig belastningsutjämnare >> **alla inställningar**.
4. Bläddra till höger i dialogrutan under namnet på belastningsutjämnaren **övervakning**, klickar du på **diagnostik**.

    ![Portal –--inställningarna för belastningsutjämnaren](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. I den **diagnostik** rutan under **Status**väljer **på**.
6. Klicka på **Lagringskonto**.
7. Under **loggar**, Välj ett befintligt lagringskonto eller skapa en ny. Använd skjutreglaget för att avgöra hur många dagar med händelsedata sparas i händelseloggarna. 
8. Klicka på **Spara**.

    ![Portal - diagnostik loggar](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Granskningsloggar kräver inte ett separat lagringskonto. Användning av lagring för händelsen och hälsa avsökningen loggning kommer avgifter service.

## <a name="audit-log"></a>Granskningslogg

Granskningsloggen skapas som standard. Loggarna finns kvar i 90 dagar i Azures händelseloggar store. Mer information om de här loggarna genom att läsa den [visa händelser och granskningsloggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) artikel.

## <a name="alert-event-log"></a>Aviseringen händelseloggen

Den här loggen genereras bara om du har aktiverat på en per belastningen belastningsutjämnaren basis. Händelser loggas i JSON-format och lagras i storage-konto som du angav när du har aktiverat loggning. Följande är ett exempel på en händelse.

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

JSON-utdata i *eventname* -egenskap som beskriver anledningen till belastningsutjämnaren skapas en avisering. I det här fallet berodde aviseringen genereras på TCP-port resursuttömning på grund av källa IP NAT gränser (SNAT).

## <a name="health-probe-log"></a>Hälsotillstånd avsökningen logg

Den här loggen genereras bara om du har aktiverat på en per belastningen belastningsutjämnaren bas enligt anvisningarna ovan. Data lagras i storage-konto som du angav när du har aktiverat loggning. En behållare med namnet 'insikter-loggar loadbalancerprobehealthstatus' har skapats och följande data loggas:

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

JSON-utdata som visas i egenskapsfältet grundläggande information om hälsostatus för avsökning. Den *dipDownCount* egenskapen visar det totala antalet instanser på backend som inte tar emot nätverkstrafik på grund av misslyckad avsökningen svar.

## <a name="view-and-analyze-the-audit-log"></a>Visa och analysera granskningsloggen

Du kan visa och analysera granskningsdata med någon av följande metoder:

* **Azure-verktyg:** hämta information från granskningsloggarna via Azure PowerShell, Azure-kommandoradsgränssnittet (CLI), Azure REST API eller Azure preview portal. Stegvisa instruktioner för varje metod beskrivs i den [granskningsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md) artikel.
* **Powerbi:** om du inte redan har en [Power BI](https://powerbi.microsoft.com/pricing) konto, du kan testa det gratis. Med hjälp av den [Azure-granskningsloggarna content pack för Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), du kan analysera dina data med förkonfigurerade instrumentpaneler och du kan anpassa vyer så att de passar dina behov.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visa och analysera hälsoavsökningen och händelseloggen

Du måste ansluta till ditt lagringskonto och hämta JSON-loggposter för händelsen och hälsa avsökningen loggar. När du hämtar JSON-filer kan du omvandla dem till CSV och visa i Excel, PowerBI eller andra data visualiseringen verktyg.

> [!TIP]
> Om du är bekant med Visual Studio och grundläggande begrepp för att ändra värdena för variabler i C# och konstanter, kan du använda den [logga konverteraren verktyg](https://github.com/Azure-Samples/networking-dotnet-log-converter) tillgängliga från GitHub.

## <a name="additional-resources"></a>Ytterligare resurser

* [Visualisera dina Azure-granskningsloggarna med Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogginlägg.
* [Visa och analysera Azure-granskningsloggar i Power BI och mycket mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

## <a name="next-steps"></a>Nästa steg

[Förstå avsökningar av belastningsutjämnare](load-balancer-custom-probe-overview.md)
