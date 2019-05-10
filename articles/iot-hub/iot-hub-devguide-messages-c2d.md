---
title: Förstå Azure IoT Hub moln-till-enhet-meddelanden | Microsoft Docs
description: Developer guide – hur du använder molnet till enhet-meddelanden med IoT Hub. Innehåller information om meddelandet livscykel och konfigurationsalternativ.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: b0c1b877a9468ce9c3b851bce62cb87c64c04260
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472743"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Skicka meddelanden från moln till enhet från IoT Hub

Om du vill skicka enkelriktade meddelanden till enhetsappen från lösningens backend-servrar, skicka du molnet till enheter meddelanden från IoT hub till enheten. En beskrivning av andra alternativ för molnet till enheter som stöds av IoT Hub finns i [moln till enhet kommunikation vägledning](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden från molnet till enheten via en tjänst-riktade slutpunkt (**/meddelanden/devicebound**). En enhet sedan ta emot meddelanden via en enhetsspecifik slutpunkt (**/devices/ {deviceId} / meddelanden/devicebound**).

Om du vill anpassa varje moln-till-enhet-meddelande vid en enskild enhet, IoT Hub anger den **till** egenskap **/devices/ {deviceId} / meddelanden/devicebound**.

Varje enhet kön innehåller högst 50 meddelanden från molnet till enheten. Försök att skicka fler meddelanden till samma enhet resulterar i ett fel.

## <a name="the-cloud-to-device-message-lifecycle"></a>Livscykel för moln till enhet-meddelande

IoT Hub lagrar meddelanden från molnet till enheten i enhetsspecifika köer för att garantera leverans av meddelanden på minst en gång. Enheter måste uttryckligen godkänna *slutförande* för IoT Hub för att ta bort dem från kön. Den här metoden garanterar skydd mot anslutning och vid fel.

Följande diagram visar livscykeln tillstånd graph för ett moln-till-enhet-meddelande i IoT Hub.

![Livscykel för moln till enhet-meddelande](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

När tjänsten IoT Hub skickar ett meddelande till en enhet, tjänsten anger Meddelandetillstånd till **i kö**. När en enhet försöker *får* ett meddelande, IoT Hub *Lås* meddelandet (genom att ställa in tillståndet **osynliga**), vilket gör att andra trådar på enheten för att börja ta emot andra meddelanden. När en enhet tråd slutförs bearbetningen av ett meddelande skickas ett meddelande till IoT Hub med *slutföra* meddelandet. IoT Hub anger sedan statusen till **slutförd**.

En enhet kan också välja att:

* *Avvisa* meddelandet, vilket gör att IoT Hub för att den **död lettered** tillstånd. Enheter som ansluter via MQTT-protokollet kan inte avvisa meddelanden från molnet till enheten.

* *Lämna* meddelandet, vilket gör att IoT Hub för att placera meddelandet i kön, med statusen inställd på **i kö**. Enheter som ansluter via MQTT-protokollet kan inte lämna meddelanden från molnet till enheten.

En tråd kan misslyckas med att bearbeta ett meddelande utan att meddela IoT Hub. I det här fallet meddelanden automatiskt övergång från den **osynliga** tillstånd tillbaka till den **i kö** tillstånd efter en *synlighet (eller lås) timeout*. Standardvärdet för den här timeouten är en minut.

Den **max antal leveranser** -egenskapen i IoT Hub avgör hur många gånger ett meddelande kan överföras mellan den **i kö** och **osynliga** tillstånd. Efter det antalet övergångar, IoT Hub ställer in tillståndet för meddelandet att **död lettered**. På samma sätt, IoT Hub anger tillståndet för ett meddelande till **död lettered** efter dess förfallotid (se [livslängd](#message-expiration-time-to-live)).

Den [hur du skickar meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) visar hur du skickar meddelanden från moln till enhet från molnet och tar emot dem på en enhet.

En enhet fyller vanligtvis ett moln-till-enhet-meddelande när förlusten av meddelandet inte påverkar programlogiken. Exempelvis kan köras när enheten har sparat meddelandet innehåll lokalt eller har en åtgärd. Meddelandet kan också innehålla tillfälliga uppgifter, vars förlust inte påverkar funktionerna i programmet. För långvariga aktiviteter kan du ibland:

* Slutför moln-till-enhet-meddelande när spara uppgiftsbeskrivning i lokal lagring.

* Meddela lösningens backend-server med en eller flera meddelanden från enheten till molnet i olika faser av förloppet för uppgiften.

## <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (tid till live)

Alla moln-till-enhet-meddelanden har en förfallotid. Nu har angetts av en av:

* Den **ExpiryTimeUtc** -egenskapen i tjänsten.
* IoT Hub med standard *livslängd* anges som en IoT Hub-egenskap.

Se [moln till enhet konfigurationsalternativ](#cloud-to-device-configuration-options).

Ett vanligt sätt att dra nytta av förfallodatum för meddelanden och undvika att är skicka meddelanden till frånkopplade enheter att ställa in kort tid till live-värden. Den här metoden ger samma resultat som upprätthålla anslutningen Enhetsstatus, samtidigt som det är mer effektivt. När du begär meddelande bekräftelser meddelar vilka enheter som är i IoT Hub:

* Det går att ta emot meddelanden.
* Inte är online eller har misslyckats.

## <a name="message-feedback"></a>Meddelande-feedback

När du skickar ett moln-till-enhet-meddelande, kan tjänsten begära leveransen av per meddelande feedback gällande sluttillstånd i meddelandet. Detta görs genom att ange den `iothub-ack` programegenskap i C2D-meddelandet som skickas till något av följande värden:

| Ack-egenskapsvärdet | Beteende |
| ------------ | -------- |
| **Ingen**     | IoT Hub genererar inte ett feedback-meddelande (standardinställning). |
| **positivt** | Om moln-till-enhet-meddelande når den **slutförd** tillstånd, IoT Hub genererar ett feedback-meddelande. |
| **negative** | Om moln-till-enhet-meddelande når den **död lettered** tillstånd, IoT Hub genererar ett feedback-meddelande. |
| **Fullständig**     | IoT Hub genererar ett feedback-meddelande i båda fallen. |

Om **Ack** är **fullständig**, och du inte får ett meddelande om feedback, betyder det att feedback meddelandet har gått ut. Tjänsten kan inte vet vad hände med det ursprungliga meddelandet. En tjänst bör i praktiken, se till att den kan bearbeta feedback innan den upphör. Den längsta förfallotiden som två dagar, vilket lämnar tid att hämta tjänsten körs igen om ett fel inträffar.

Enligt beskrivningen i [slutpunkter](iot-hub-devguide-endpoints.md), IoT-hubb levererar feedback via en tjänst-riktade slutpunkt (**/messages/servicebound/feedback**) som meddelanden. Semantik för att ta emot feedback är desamma som för meddelanden från molnet till enheten. När det är möjligt batchar meddelande feedback i ett enda meddelande med följande format:

| Egenskap      | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | Tidsstämpel som visar när feedback-meddelande togs emot av hubben. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Brödtexten är en JSON-serialiserat matris med poster, var och en med följande egenskaper:

| Egenskap            | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Tidsstämpel som visar när resultatet av meddelandet har inträffat. Till exempel hubben fick meddelandet feedback eller det ursprungliga meddelandet har gått ut. |
| OriginalMessageId  | **MessageId** meddelandets moln-till-enhet som den här informationen för feedback avser. |
| StatusCode         | Strängen som krävs. Används i feedbackmeddelanden som genereras av IoT Hub. <br/> ”Lyckades” <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> ”Rensas” |
| Beskrivning        | Sträng värden för **StatusCode**. |
| DeviceId           | **DeviceId** målenhetens meddelandets moln-till-enhet som den här typ av feedback avser. |
| DeviceGenerationId | **DeviceGenerationId** målenhetens meddelandets moln-till-enhet som den här typ av feedback avser. |

Tjänsten måste ange en **MessageId** för moln-till-enhet-meddelande för att kunna korrelera dess feedback med det ursprungliga meddelandet.

I följande exempel visas texten i ett feedback-meddelande.

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

| Egenskap                   | Beskrivning | Intervall- och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för meddelanden från molnet till enheten. | ISO_8601 intervall upp till 2D (minst 1 minut). Standard: 1 timme. |
| maxDeliveryCount          | Maximalt antal leveranser för moln-till-enhet per enhet köer. | 1 och 100. Standard: 10. |
| feedback.ttlAsIso8601     | Kvarhållning för service-bunden meddelanden. | ISO_8601 intervall upp till 2D (minst 1 minut). Standard: 1 timme. |
| feedback.maxDeliveryCount |Maximalt antal leveranser för någon feedbackkö. | 1 och 100. Standard: 100. |

Mer information om hur du anger dessa konfigurationsalternativ finns i [skapa IoT-hubbar](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Nästa steg

Information om SDK: erna som du kan använda för att ta emot meddelanden från moln till enhet finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

Om du vill prova ta emot meddelanden från moln till enhet finns i den [skicka moln till enhet](iot-hub-csharp-csharp-c2d.md) självstudien.