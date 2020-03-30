---
title: Leverans och återförsök – Azure Event Grid IoT Edge | Microsoft-dokument
description: Leverans och återförsök i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841720"
---
# <a name="delivery-and-retry"></a>Leverans och nytt försök

Event Grid ger hållbar leverans. Den försöker leverera varje meddelande minst en gång för varje matchande prenumeration omedelbart. Om en prenumerantslutpunkt inte bekräftar mottagandet av en händelse eller om det är fel, försöker Event Grid leverera baserat på en **fast schema för återförsök** och **återförsök.**  Som standard levererar modulen Event Grid en händelse i taget till prenumeranten. Nyttolasten är dock en matris med en enda händelse. Du kan låta modulen leverera mer än en händelse i taget genom att aktivera batchfunktionen för utdata. Mer information om den här funktionen finns i [utdatabatching](delivery-output-batching.md).  

> [!IMPORTANT]
>Det finns inget stöd för händelsedata. Det innebär att omfördelning eller omstart av modulen Event Grid gör att du förlorar alla händelser som ännu inte har levererats.

## <a name="retry-schedule"></a>Schema för återförsök

Event Grid väntar upp till 60 sekunder på ett svar efter att ha levererat ett meddelande. Om abonnentens slutpunkt inte ACK svaret, då meddelandet kommer att dämpas i en av våra back off köer för efterföljande återförsök.

Det finns två förkonfigurerade avbackade köer som avgör det schema som ett nytt försök ska göras på. De är:

| Schema | Beskrivning |
| ---------| ------------ |
| 1 minut | Meddelanden som hamnar här görs varje minut.
| 10 minuter | Meddelanden som hamnar här försöker var tionde minut.

### <a name="how-it-works"></a>Hur det fungerar

1. Meddelandet kommer till modulen Event Grid. Man försöker leverera den omedelbart.
1. Om leveransen misslyckas, då meddelandet är enqueued i 1-minuters kö och försöker igen efter en minut.
1. Om leveransen fortsätter att misslyckas, då meddelandet är enqueued i 10-minuters kö och försökte igen var 10 minuter.
1. Leveranserna görs tills principbegränsningar eller återförsök har uppnåtts.

## <a name="retry-policy-limits"></a>Begränsningar för återförsöksprincip

Det finns två konfigurationer som bestämmer principen för återförsök. De är:

* Maximalt antal försök
* Händelsetid för att leva (TTL)

En händelse tas bort om någon av gränserna för återförsöksprincipen har nåtts. Själva schemat för återförsök beskrevs i avsnittet Schemalägg igen. Konfiguration av dessa gränser kan göras antingen för alla prenumeranter eller per prenumerationsbas. I följande avsnitt beskrivs var och en av dem ytterligare detaljer.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurera standardvärden för alla prenumeranter

Det finns två `brokers__defaultMaxDeliveryAttempts` `broker__defaultEventTimeToLiveInSeconds` egenskaper: och som kan konfigureras som en del av distributionen av händelserutnät, som styr principen för återförsök för alla prenumeranter.

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärde: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Händelse TTL i sekunder varefter en händelse kommer att släppas om den inte levereras. Standardvärde: **7200** sekunder

## <a name="configuring-defaults-per-subscriber"></a>Konfigurera standardvärden per prenumerant

Du kan också ange principbegränsningar för återförsök per prenumeration.
Mer information om hur du konfigurerar standardvärden per prenumerant finns i vår [API-dokumentation.](api.md) Standardinställningar på prenumerationsnivå åsidosätter konfigurationerna på modulnivå.

## <a name="examples"></a>Exempel

I följande exempel ställs princip för återförsök i modulen Event Grid med maxNumberOfAttempts = 3 och Event TTL på 30 minuter

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

I följande exempel ställs en webbkrokprenumeration med maxNumberOfAttempts = 3 och Event TTL på 30 minuter

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
