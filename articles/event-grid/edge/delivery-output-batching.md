---
title: Utdatabatchning i Azure Event Grid IoT Edge | Microsoft-dokument
description: Utdatabatchning i händelserutnät på IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841755"
---
# <a name="output-batching"></a>Batchbearbetning av utdata

Event Grid har stöd för att leverera mer än en händelse i en enda leveransbegäran. Den här funktionen gör det möjligt att öka det totala leveransflödet utan att betala http per begäran omkostnader. Batchbearbetning är inaktiverat som standard och kan aktiveras per prenumeration.

> [!WARNING]
> Den maximala tillåtna varaktigheten för att bearbeta varje leveransbegäran ändras inte, även om prenumerantkoden potentiellt måste utföra mer arbete per batchad begäran. Leveranstidsgränsen är som standard 60 sekunder.

## <a name="batching-policy"></a>Batchningsprincip

Event Grids batchbeteende kan anpassas per prenumerant genom att justera följande två inställningar:

* Maximala händelser per parti

  Den här inställningen anger en övre gräns för antalet händelser som kan läggas till i en batchad leveransbegäran.

* Önskad batchstorlek i Kilobyte

  Denna ratt används för att ytterligare styra det maximala antalet kilobyte som kan skickas över per leveransbegäran

## <a name="batching-behavior"></a>Batching-beteende

* Alla eller inga

  Event Grid fungerar med allt-eller-ingen semantik. Det stöder inte partiell framgång för en batchleverans. Prenumeranter bör vara noga med att bara be om så många händelser per parti som de rimligen kan hantera i 60 sekunder.

* Optimistisk batching

  Batchprincipinställningarna är inte strikta gränser för batchningsbeteendet och respekteras på bästa sätt. Vid låga händelsefrekvenser observerar du ofta att batchstorleken är mindre än de begärda maximala händelserna per batch.

* Standard är inställt på OFF

  Som standard lägger Event Grid bara till en händelse i varje leveransbegäran. Sättet att aktivera batchbearbetning är att ange någon av de inställningar som nämns tidigare i artikeln i händelseprenumerationen JSON.

* Standardvärden

  Det är inte nödvändigt att ange båda inställningarna (Högsta händelser per batch och Ungefärlig batchstorlek i kilobyte) när du skapar en händelseprenumeration. Om bara en inställning har angetts används (konfigurerbara) standardvärden (konfigurerbara). Se följande avsnitt för standardvärdena och hur du åsidosätter dem.

## <a name="turn-on-output-batching"></a>Aktivera batchbearbetning av utdata

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Konfigurera högsta tillåtna värden

Följande inställningar för distributionstid styr det högsta tillåtna värdet när en händelseprenumeration skapas.

| Egenskapsnamn | Beskrivning |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maximalt värde som `PreferredBatchSizeInKilobytes` tillåts för vredet. Standard `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximalt värde som `MaxEventsPerBatch` tillåts för vredet. Standard `50`.

## <a name="configuring-runtime-default-values"></a>Konfigurera standardvärden för körning

Följande inställningar för distributionstid styr standardvärdet för körning för varje knapp när det inte anges i händelseprenumerationen. Om du vill upprepa måste minst en knapp ställas in på händelseprenumerationen för att aktivera batchningsbeteende.

| Egenskapsnamn | Beskrivning |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximal storlek för `MaxEventsPerBatch` leveransbegäran när endast anges. Standard `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maximalt antal händelser som ska läggas `MaxBatchSizeInBytes` till i en batch när endast anges. Standard `10`.
