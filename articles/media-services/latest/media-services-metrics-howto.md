---
title: Visa mått med Azure Monitor
description: Den här artikeln visar hur du övervakar mått med Azure Portal-diagram och Azure CLI.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 154e5b5d9639203810e9d16dec4e2907fe5ee80a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504304"
---
# <a name="monitor-media-services-metrics"></a>Övervaka Media Services-mått

Med [Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program presterar. Detaljerad beskrivning av den här funktionen och se varför du vill använda Azure Media Services mått och diagnostikloggar finns i [övervaka Media Services statistik och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av Azure CLI. Den här artikeln visar hur du övervakar mått med Azure Portal-diagram och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./create-account-howto.md)
- Granska [övervaknings Media Services mått och diagnostikloggar](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Visa mått i Azure Portal

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till ditt Azure Media Services konto och välj **mått**.
1. Klicka på rutan **resurs** och välj den resurs som du vill övervaka mått för.

    Fönstret **Välj en resurs** visas till höger med listan över resurser som är tillgängliga för dig. I det här fallet visas:

    * &lt;Media Services konto namn&gt;
    * &lt;Media Services konto namn &gt; / &lt; strömmande slut punkts namn&gt;
    * &lt;lagrings konto namn&gt;

    Markera resursen och tryck på **Använd**. Mer information om resurser och mått som stöds finns i [övervaka Media Services mått](media-services-metrics-diagnostic-logs.md).

    ![Mått](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Om du vill växla mellan resurser som du vill övervaka måtten för, klickar du på **resurs** rutan igen och upprepar det här steget.
1. (Valfritt) ge diagrammet ett namn (redigera namnet genom att trycka på Penn fönstret överst).
1. Lägg till mått som du vill visa.

    ![Mått](media/media-services-metrics/metrics03.png)
1. Du kan fästa diagrammet på instrument panelen.

## <a name="view-metrics-with-azure-cli"></a>Visa mått med Azure CLI

Om du vill hämta mått för "utgående" med Azure CLI kör du följande `az monitor metrics` kommando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Om du vill ha andra mått ersätter du "utgående" för det mått namn som du är intresse rad av.

## <a name="see-also"></a>Se även

* [Azure Monitor mått](../../azure-monitor/platform/data-platform.md)
* [Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg

[Diagnostikloggar](media-services-diagnostic-logs-howto.md)
