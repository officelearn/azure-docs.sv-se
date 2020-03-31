---
title: Visa mått med Azure Monitor
description: Den här artikeln visar hur du övervakar mått med Azure-portaldiagram och Azure CLI.
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382944"
---
# <a name="monitor-media-services-metrics"></a>Övervaka Media Services-mått

[Med Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program fungerar. Detaljerad beskrivning av den här funktionen och för att se varför du vill använda Azure Media Services-mått och diagnostikloggar finns i [Övervaka Media Services-mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

Azure Monitor innehåller flera sätt att interagera med mått, inklusive att kartlägga dem i portalen, komma åt dem via REST API eller fråga dem med Azure CLI. Den här artikeln visar hur du övervakar mått med Azure-portaldiagram och Azure CLI.

## <a name="prerequisites"></a>Krav

- [Skapa ett Media Services-konto](create-account-cli-how-to.md)
- Granska [Mätvärden för Monitor Media Services och diagnostikloggar](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visa mått i Azure-portalen

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till ditt Azure Media Services-konto och välj **Mått**.
1. Klicka **på** resursrutan och välj den resurs som du vill övervaka mått för.

    Fönstret **Välj en resurs** visas till höger med listan över resurser som är tillgängliga för dig. I det här fallet ser du:

    * &lt;Kontonamn för Media Services&gt;
    * &lt;Slutpunktsnamn&gt;/&lt;för direktuppspelning av Media Services-kontonamn&gt;
    * &lt;namn på lagringskonto&gt;

    Markera resursen och tryck på **Använd**. Mer information om resurser och mått som stöds finns i [Övervaka mediatjänstmått](media-services-metrics-diagnostic-logs.md).

    ![Mått](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Om du vill växla mellan resurser som du vill övervaka mått för klickar du på **resursrutan** igen och upprepar det här steget.
1. (Alternativt) ge ditt diagram ett namn (redigera namnet genom att trycka på pennan högst upp).
1. Lägg till mått som du vill visa.

    ![Mått](media/media-services-metrics/metrics03.png)
1. Du kan fästa diagrammet på instrumentpanelen.

## <a name="view-metrics-with-azure-cli"></a>Visa mått med Azure CLI

Om du vill hämta Egress-mått med Azure `az monitor metrics` CLI kör du följande kommando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Om du vill hämta andra mått ersätter du "Egress" med det måttnamn du är intresserad av.

## <a name="see-also"></a>Se även

* [Azure-övervakarmått](../../azure-monitor/platform/data-platform.md)
* [Skapa, visa och hantera måttaviseringar med Hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg

[Diagnostiska loggar](media-services-diagnostic-logs-howto.md)
