---
title: Visa mått med Azure Monitor
description: Den här artikeln visar hur du övervakar mått med Azure portal diagram och Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795831"
---
# <a name="monitor-media-services-metrics"></a>Övervaka Media Services-mått 

[Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program utför. Detaljerad beskrivning av den här funktionen och varför du skulle vilja använda Azure Media Services mått och diagnostik för loggar finns i [övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

Azure Monitor innehåller flera olika sätt att interagera med mätvärden, inklusive diagram dem i portalen, få åtkomst till dem via REST API eller frågor till dem med hjälp av CLI. Den här artikeln visar hur du övervakar mått med Azure portal diagram och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](create-account-cli-how-to.md)
- Granska [övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visa mått i Azure-portalen

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till ditt Azure Media Services-konto och välj **mått**.
1. Klicka på den **RESOURCE** rutan och välj den resurs som du vill övervaka mått. 

    Den **väljer du en resurs** visas till höger med listan över resurser som är tillgängliga för dig. I det här fallet visas 

    * &lt;Media Services-kontonamn&gt;
    * &lt;Media Services-kontonamn&gt;/&lt;namnet på slutpunkten för direktuppspelning&gt;
    * &lt;Lagringskontonamn&gt;

    Välj den resurs och tryck på **tillämpa**. Mer information om resurser som stöds och mått finns [övervaka Media Services mått](media-services-metrics-diagnostic-logs.md).
 
    ![Mått](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Om du vill växla mellan resurser som du vill övervaka mått, klickar du på den **RESOURCE** rutan igen och upprepa det här steget.
1. (Valfritt) Namnge ditt diagram (Redigera namnet genom att trycka på blyertspennan högst upp).
1. Lägg till mått som du vill visa.

    ![Mått](media/media-services-metrics/metrics03.png)
1. Du kan fästa diagrammet på instrumentpanelen.

## <a name="view-metrics-with-azure-cli"></a>Visa mått med Azure CLI

Om du vill ha ”utgående” mått med CLI kör du följande `az monitor metrics` CLI-kommando:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Ersätta ”utgående” Måttnamn som du vill veta om du vill ha andra mått.

## <a name="see-also"></a>Se också

* [Azure Monitor-mått](../../azure-monitor/platform/data-platform.md)
* [Skapa, visa och hantera aviseringar för mått med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg

[Diagnostikloggar](media-services-diagnostic-logs-howto.md)
