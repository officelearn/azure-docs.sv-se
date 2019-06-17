---
title: Förstå Azure IoT Hub moln-till-enhet-meddelanden | Microsoft Docs
description: Den här utvecklarguide beskrivs hur du använder molnet till enhet-meddelanden med IoT-hubben. Den innehåller information om livscykeln för meddelande- och konfigurationsalternativ.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055327"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Skicka meddelanden från moln till enhet från en IoT-hubb

Om du vill skicka enkelriktade meddelanden till en enhet från lösningens backend-servrar måste du skicka meddelanden från moln till enhet från IoT hub till din enhet. En beskrivning av andra alternativ för moln-till-enhet som stöds av Azure IoT Hub finns i [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden från molnet till enheten via en tjänst-riktade slutpunkt, */meddelanden/devicebound*. En enhet sedan ta emot meddelanden via en enhetsspecifik slutpunkt, */devices/ {deviceId} / meddelanden/devicebound*.

Om du vill anpassa varje moln-till-enhet-meddelande vid en enskild enhet, din IoT-hubb anger den **till** egenskap */devices/ {deviceId} / meddelanden/devicebound*.

Varje enhet kön innehåller högst 50 meddelanden från molnet till enheten. Att skicka fler meddelanden till samma enhet resulterar i ett fel.

## <a name="the-cloud-to-device-message-life-cycle"></a>Livscykel för moln till enhet-meddelande

Håller kvar meddelanden från molnet till enheten i enhetsspecifika köer för att garantera på minst en gång meddelandeleverans, IoT-hubben. För IoT-hubben att ta bort meddelanden från kön enheterna måste uttryckligen godkänna *slutförande*. Den här metoden garanterar skydd mot anslutning och vid fel.

Livscykeln tillstånd diagrammet visas i följande diagram:

![Livscykel för moln till enhet-meddelande](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

När IoT hub-tjänsten skickar ett meddelande till en enhet, tjänsten anger Meddelandetillstånd till *i kö*. När en enhet försöker *får* ett meddelande, IoT-hubben *Lås* meddelandet genom att ställa in tillståndet *osynliga*. Det här tillståndet kan andra trådar på enheten för att börja ta emot andra meddelanden. När en enhet tråd slutförs bearbetningen av ett meddelande skickas ett meddelande till IoT-hubben genom *slutföra* meddelandet. IoT-hubben anger sedan statusen till *slutförd*.

En enhet kan också:

* *Avvisa* meddelandet, vilket gör att IoT hub för att den *död lettered* tillstånd. Enheter som ansluter via protokollet Message Queuing Telemetry Transport (MQTT) kan inte avvisa meddelanden från molnet till enheten.

* *Lämna* meddelandet, vilket gör att IoT hub att placera meddelandet i kön, med statusen inställd på *i kö*. Enheter som ansluter via MQTT-protokollet kan inte lämna meddelanden från molnet till enheten.

En tråd kan misslyckas med att bearbeta ett meddelande utan att meddela IoT-hubben. I det här fallet meddelanden automatiskt övergång från den *osynliga* tillstånd tillbaka till den *i kö* tillstånd efter en *synlighet* timeout (eller *Lås* timeout). Standardvärdet för timeout är en minut.

Den **max antal leveranser** egenskapen på IoT-hubben avgör hur många gånger ett meddelande kan överföras mellan den *i kö* och *osynliga* tillstånd. Efter det antalet övergångar, IoT-hubben ställer in tillståndet för meddelandet att *död lettered*. På samma sätt, IoT-hubben anger tillståndet för ett meddelande till *död lettered* efter dess förfallotid. Mer information finns i [livslängd](#message-expiration-time-to-live).

Den [hur du skickar meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) artikeln visar hur du skickar meddelanden från moln till enhet från molnet och tar emot dem på en enhet.

En enhet fyller normalt ett moln-till-enhet-meddelande när förlusten av meddelandet inte påverkar programlogiken. Ett exempel på detta kan vara när enheten har sparat meddelandeinnehåll lokalt eller har kört en åtgärd. Meddelandet kan också innehålla tillfälliga uppgifter, vars dataförlust skulle påverka funktionaliteten i programmet. För långvariga aktiviteter kan du ibland:

* Komplettera meddelandet moln-till-enhet när enheten har sparat uppgiftsbeskrivning i lokal lagring.

* Meddela lösningens backend-server med en eller flera meddelanden från enheten till molnet i olika faser av förloppet för uppgiften.

## <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (tid till live)

Alla moln-till-enhet-meddelanden har en förfallotid. Den här tiden anges med någon av följande:

* Den **ExpiryTimeUtc** -egenskapen i tjänsten
* IoT-hubben med hjälp av standard *livslängd* som har angetts som en IoT hub-egenskap

Se [moln till enhet konfigurationsalternativ](#cloud-to-device-configuration-options).

Ett vanligt sätt att dra nytta av ett förfallodatum för meddelanden och för att undvika att skicka meddelanden till frånkopplade enheter är att ställa in kort *livslängd* värden. Den här metoden ger samma resultat som upprätthålla anslutningen enhetstillstånd, men det är mer effektivt. När du begär meddelande bekräftelser meddelar vilka enheter som är i IoT hub:

* Det går att ta emot meddelanden.
* Inte är online eller har misslyckats.

## <a name="message-feedback"></a>Meddelande-feedback

När du skickar ett moln-till-enhet-meddelande, kan tjänsten begära leveransen av per meddelande feedback om sluttillstånd i meddelandet. Du gör detta genom att ange den **iothub-ack** programegenskap i moln-till-enhet-meddelande som skickas till någon av följande fyra värden:

| Ack-egenskapsvärdet | Beteende |
| ------------ | -------- |
| Ingen     | IoT hub generera inte ett feedback-meddelande (standardinställning). |
| positivt | Om moln-till-enhet-meddelande når den *slutförd* tillstånd, IoT-hubben genererar ett feedback-meddelande. |
| negativt | Om moln-till-enhet-meddelande når den *död lettered* tillstånd, IoT-hubben genererar ett feedback-meddelande. |
| fullständig     | IoT-hubben genererar ett feedback-meddelande i båda fallen. |

Om den **Ack** värdet är *fullständig*, och du inte får ett meddelande om feedback, betyder det att feedback meddelandet har gått ut. Tjänsten kan inte vet vad hände med det ursprungliga meddelandet. En tjänst bör i praktiken, se till att den kan bearbeta feedback innan den upphör. Den maximala förfallotiden två dagar, vilket lämnar tid att hämta tjänsten körs igen om ett fel inträffar.

Enligt beskrivningen i [slutpunkter](iot-hub-devguide-endpoints.md), IoT-hubb levererar feedback via en tjänst-riktade slutpunkt, */messages/servicebound/feedback*, som meddelanden. Semantik för att ta emot feedback är desamma som för meddelanden från molnet till enheten. När det är möjligt batchar meddelande feedback i ett enda meddelande med följande format:

| Egenskap     | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | En tidsstämpel som anger när feedback-meddelande togs emot av hubben |
| Användar-ID       | `{iot hub name}` |
| contentType  | `application/vnd.microsoft.iothub.feedback.json` |

Brödtexten är en JSON-serialiserat matris med poster, var och en med följande egenskaper:

| Egenskap           | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | En tidsstämpel som anger när resultatet av meddelandet har inträffat (till exempel hubben fick meddelandet feedback eller det ursprungliga meddelandet har gått ut) |
| OriginalMessageId  | Den *MessageId* meddelandets moln-till-enhet som den här informationen för feedback avser |
| StatusCode         | En obligatorisk sträng som används i meddelanden som genereras av IoT hub: <br/> *Lyckades* <br/> *Har upphört att gälla* <br/> *DeliveryCountExceeded* <br/> *Avvisade* <br/> *Rensas* |
| Beskrivning        | Sträng värden för *StatusCode* |
| DeviceId           | Den *DeviceId* målenhetens meddelandets moln-till-enhet som den här typ av feedback avser |
| DeviceGenerationId | Den *DeviceGenerationId* målenhetens meddelandets moln-till-enhet som den här typ av feedback avser |

För moln-till-enhet-meddelande att korrelera dess feedback med det ursprungliga meddelandet tjänsten måste ange en *MessageId*.

Texten i ett feedback-meddelande visas i följande kod:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Konfigurationsalternativ för moln-till-enhet

Varje IoT-hubb exponerar följande konfigurationsalternativ för moln-till-enhet-meddelanden:

| Egenskap                  | Beskrivning | Intervall- och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för meddelanden från moln till enhet | ISO_8601 intervall upp till två dagar (minst 1 minut); standard: 1 timme |
| maxDeliveryCount          | Maximalt antal leveranser för moln-till-enhet per enhet köer | 1 till 100; standard: 10 |
| feedback.ttlAsIso8601     | Kvarhållning för service-bunden meddelanden | ISO_8601 intervall upp till två dagar (minst 1 minut); standard: 1 timme |
| feedback.maxDeliveryCount | Maximalt antal leveranser för kön feedback | 1 till 100; standard: 100 |

Mer information om hur du anger dessa konfigurationsalternativ finns i [skapa IoT-hubbar](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Nästa steg

Information om SDK: er som du kan använda för att ta emot meddelanden från moln till enhet finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

Om du vill prova ta emot meddelanden från moln till enhet finns i den [skicka moln till enhet](iot-hub-csharp-csharp-c2d.md) självstudien.
