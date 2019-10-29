---
title: Leverans och nya försök Azure Event Grid IoT Edge | Microsoft Docs
description: Leverans och försök igen i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0a678023b1097c4bdec70d866632da6ae4ad57bb
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992397"
---
# <a name="delivery-and-retry"></a>Leverans och nytt försök

Event Grid tillhandahåller varaktig leverans. Det försöker leverera varje meddelande minst en gång för varje matchande prenumeration direkt. Om en prenumerants slut punkt inte bekräftar mottagandet av en händelse eller om det uppstår ett fel, Event Grid försöker leverera igen baserat på ett fast **schema för omförsök** och **försök igen**.  För närvarande Event Grid modulen leverera en händelse i taget till prenumeranten. Nytto lasten är dock en matris med en enda händelse.

> [!IMPORTANT]
>Det finns inget beständigt stöd för händelse data. Det innebär att omdistribution eller omstart av Event Grid-modulen gör att du förlorar eventuella händelser som ännu inte har levererats.

## <a name="retry-schedule"></a>Schema för förnyat försök

Event Grid väntar upp till 60 sekunder innan ett meddelande har levererats. Om prenumerantens slut punkt inte ACK ger svar, kommer meddelandet att placeras i en av våra säkerhets kopierings köer för efterföljande återförsök.

Det finns två förkonfigurerade köer som avgör vilket schema som försök görs att försöka igen. De är:-

| Schema | Beskrivning |
| ---------| ------------ |
| 1 minut | Meddelanden som visas här görs varje minut.
| 10 minuter | Meddelanden som visas här kommer att göras var tionde minut.

### <a name="how-it-works"></a>Så här fungerar det

1. Meddelandet tas emot i Event Grid-modulen. Försök görs att leverera det direkt.
1. Om leveransen Miss lyckas placeras meddelandet i kö i 1 minut och provas igen efter en minut.
1. Om leveransen fortsätter att fungera hamnar meddelandet i kö i 10 minuter och provas var 10: e minut.
1. Leveranser görs till dess att de slutförda eller återförsöks princip gränserna har nåtts.

## <a name="retry-policy-limits"></a>Princip begränsningar för återförsök

Det finns två konfigurationer som avgör princip för återförsök. De är:-

* Maximalt antal försök
* TTL (Time-to-Live) för händelse

En händelse tas bort om någon av gränserna för principen för återförsök har nåtts. Schemat för förnyat försök beskrivs i avsnittet schema för förnyat försök. Konfigurationen av dessa gränser kan göras antingen för alla prenumeranter eller per prenumeration. I följande avsnitt beskrivs var och en ytterligare information.

## <a name="configuring-defaults-for-all-subscribers"></a>Konfigurera standardvärden för alla prenumeranter

Det finns två egenskaper: `brokers:defaultMaxDeliveryAttempts` och `broker:defaultEventTimeToLiveInSeconds` som kan konfigureras som en del av Event Grid-distributionen, som styr återförsöks principens standardinställningar för alla prenumeranter.

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärde: 30.
| `broker:defaultEventTimeToLiveInSeconds` | TTL för händelse i sekunder efter vilken en händelse tas bort om den inte levereras. Standardvärde: **7200** sekunder

## <a name="configuring-defaults-per-subscriber"></a>Konfigurera standardinställningar per prenumerant

Du kan också ange en princip gräns för återförsök per prenumeration.
I vår [API-dokumentation](api.md) finns information om hur du konfigurerar standardinställningar per prenumerant. Standardvärden för prenumerations nivåer åsidosätter modulens nivå konfiguration.

## <a name="examples"></a>Exempel

I följande exempel konfigureras princip för återförsök i Event Grid-modulen med maxNumberOfAttempts = 3 och händelse-TTL på 30 minuter

```json
{
  "Env": [
    "broker:defaultMaxDeliveryAttempts=3",
    "broker:defaultEventTimeToLiveInSeconds=1800"
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

I följande exempel konfigureras en Web Hook-prenumeration med maxNumberOfAttempts = 3 och händelse-TTL på 30 minuter

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
