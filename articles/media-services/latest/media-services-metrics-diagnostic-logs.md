---
title: Övervaka Media Services-mått och diagnostikloggar via Azure Monitor | Microsoft Docs
description: Den här artikeln ger en översikt över hur du övervakar Media Services-mått och diagnostikloggar via Azure Monitor.
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848054"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Övervaka Media Services-mått och diagnostikloggar

[Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program utför. Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, mått och loggfiler. Du kan övervaka diagnostikloggar för Media Services och skapa aviseringar och meddelanden för insamlade mått och loggar. Du kan visualisera och analysera data mått med [måttutforskaren](../../azure-monitor/platform/metrics-getting-started.md). Du kan skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/), eller använda tjänster med 3 part.

Detaljerad översikt finns i [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) och [Azure Monitor diagnostiska loggar](../../azure-monitor/platform/diagnostic-logs-overview.md).

Det här avsnittet beskrivs tillgängliga [Media Services mått](#media-services-metrics) och [Media Services diagnostiska loggar](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Media Services-mått

Mätvärden samlas in med jämna mellanrum oavsett värdet ändras. Det är användbart för avisering eftersom de kan samlas in ofta och en avisering kan vara drar igång snabbt med relativt enkla logik.

För närvarande följande medietjänster [Strömningsslutpunkter](https://docs.microsoft.com/rest/api/media/streamingendpoints) mått som genereras av Azure:

|Namn|Beskrivning|
|---|---|
|Begäranden|Ger information kring Totalt antal begäranden som betjänas av slutpunkten för direktuppspelning.|
|Egress|Totalt antal utgående byte. Till exempel byte som strömmas av slutpunkt för direktuppspelning.|
|Svarstid för lyckad från slutpunkt till slutpunkt| Ger information om svarstider från slutpunkt till slutpunkt för lyckade begäranden.|

Till exempel för att få ”utgående” mått med CLI kör du följande `az monitor metrics` CLI-kommando:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Information om hur du skapar måttaviseringar finns i [skapa, visa och hantera aviseringar för mått med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Media Services-diagnostikloggar

För närvarande kan få du följande diagnostiska loggar:

|Namn|Beskrivning|
|---|---|
|Viktiga leverans-tjänstbegäran|Loggar som visar nyckelleveranstjänst begär information. Mer information finns i [scheman](media-services-diagnostic-logs-schema.md).|

Om du vill aktivera lagring av diagnostiska loggar i ett Lagringskonto, kör du följande `az monitor diagnostic-settings` CLI-kommando: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Exempel:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Nästa steg 

[Så här att samla in och använda loggdata från resurserna i Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
