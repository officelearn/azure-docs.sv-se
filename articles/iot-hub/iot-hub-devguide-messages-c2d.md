---
title: Förstå Azure IoT Hub moln till enhet messaging | Microsoft Docs
description: Utvecklare guide - hur du använder moln till enhet meddelanden med IoT-hubben. Innehåller information om meddelandet livscykel och konfigurationsalternativ.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: 670cf45a48ca4b72576cedddd4678c0d569401cd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Skicka meddelanden moln till enhet från IoT-hubb

Om du vill skicka enkelriktade meddelanden i appen enheten från din lösningens serverdel, meddelanden moln-enheter från din IoT-hubb till din enhet. En beskrivning av andra alternativ för molnet till enheter som stöds av IoT-hubb finns [moln till enhet kommunikation vägledning][lnk-c2d-guidance].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden moln till enhet via en slutpunkt för service-riktade (**/meddelanden/devicebound**). En enhet sedan tar emot meddelanden via en enhetsspecifik slutpunkt (**/devices/ {deviceId} / meddelanden/devicebound**).

Om du vill rikta varje moln till enhet meddelande på en enskild enhet IoT-hubb anger den **till** egenskapen **/devices/ {deviceId} / meddelanden/devicebound**.

Varje enhet kön innehåller högst 50 moln till enhet meddelanden. Försök att skicka fler meddelanden till samma enhet resulterar i ett fel.

## <a name="the-cloud-to-device-message-lifecycle"></a>Moln-till-enhetsmeddelande livscykel

Om du vill garantera på-minst en gång meddelandeleverans kvarstår IoT-hubb moln till enhet meddelanden i köer per enhet. Enheter måste uttryckligen Bekräfta *slutförande* för IoT-hubb för att ta bort dem från kön. Den här metoden garanterar återhämtning mot anslutning och enhetsfel.

Följande diagram visar diagrammet lifecycle tillstånd för ett moln till enhet meddelande i IoT-hubb.

![Moln-till-enhetsmeddelande livscykel][img-lifecycle]

När tjänsten IoT-hubb skickar ett meddelande till en enhet, tjänsten anger tillståndet meddelandet till **köas**. När en enhet vill *får* meddelandet IoT-hubb *Lås* meddelandet (genom att ange tillståndet **osynliga**), vilket gör att andra trådar på enheten för att börja ta emot andra meddelanden. När en enhet tråd är klar bearbetning av ett meddelande skickas ett meddelande till IoT-hubb av *Slutför* meddelandet. IoT-hubb ställer in tillståndet **slutförd**.

En enhet kan också välja att:

* *Avvisa* meddelandet, vilket gör att IoT-hubben ska anges till den **död lettered** tillstånd. Enheter som ansluter via protokollet MQTT kan inte avvisa meddelanden moln till enhet.
* *Avbryt* meddelandet, vilket gör att IoT-hubb för att placera meddelandet i kön med statusen inställd på **köas**. Enheter som ansluter via protokollet MQTT kan inte avbryta moln till enhet meddelanden.

En tråd kan misslyckas med att bearbeta ett meddelande utan att meddela IoT-hubb. I det här fallet meddelanden automatiskt övergång från den **osynliga** tillbaka till den **köas** tillstånd efter en *synlighet (eller lås) timeout*. Standardvärdet för det här är en minut.

Den **max antal leverans** -egenskapen i IoT-hubb anger det maximala antalet gånger som ett meddelande kan övergå mellan den **köas** och **osynliga** tillstånd. Efter att antalet övergångar, IoT-hubb anger tillståndet för meddelandet till **död lettered**. På liknande sätt IoT-hubb anger tillståndet för ett meddelande till **död lettered** efter dess förfallotid (se [Time to live-][lnk-ttl]).

Den [hur du skickar meddelanden moln till enhet med IoT-hubben] [ lnk-c2d-tutorial] visar hur du skickar meddelanden moln till enhet från molnet och ta emot dem på en enhet.

Normalt en enhet har slutförts meddelandet moln till enhet när förlusten av meddelandet inte påverkar programlogiken. Till exempel köras när enheten har sparat meddelandet innehållet lokalt eller har en åtgärd. Meddelandet kan också utföra tillfälligt information vars förlust inte påverkar funktionerna i programmet. Ibland är för tidskrävande uppgifter kan du:

* Slutföra meddelandet moln till enhet efter beständighet Aktivitetsbeskrivningen i lokal lagring.
* Meddela lösningens serverdel med en eller flera meddelanden från enhet till moln i olika faser av förloppet för aktiviteten.

## <a name="message-expiration-time-to-live"></a>Utgångna (time to live)

Alla moln till enhet meddelanden har en förfallotid. Nu har angetts av en av:

* Den **ExpiryTimeUtc** egenskap i tjänsten.
* IoT-hubb med *time to live-* angetts som en IoT-hubb-egenskap.

Se [moln till enhet konfigurationsalternativ][lnk-c2d-configuration].

Ett vanligt sätt att dra nytta av utgångna och undvika att är skicka meddelanden till frånkopplade enheter att ställa in time to live värden-kort. Den här metoden ger samma resultat som upprätthålla anslutningen Enhetsstatus samtidigt som det är mer effektivt. När du begär meddelande bekräftelser meddelar IoT-hubb vilka enheter som är:

* Det går att ta emot meddelanden.
* Är inte online eller har misslyckats.

## <a name="message-feedback"></a>Meddelandet feedback

När du skickar ett moln till enhet, kan tjänsten begära leverans av varje meddelande feedback om sluttillstånd i meddelandet.

| Ack-egenskap | Beteende |
| ------------ | -------- |
| **Positivt** | Om meddelandet moln till enhet når den **slutförd** tillstånd, IoT-hubb genererar ett meddelande om feedback. |
| **Negativt** | Om meddelandet moln till enhet når den **död lettered** tillstånd, IoT-hubb genererar ett meddelande om feedback. |
| **fullständig**     | IoT-hubb genererar ett meddelande om feedback i båda fallen. |

Om **Ack** är **fullständig**, och inte ett meddelande feedback, innebär det att meddelandet feedback har gått ut. Tjänsten kan inte vet vad hände med det ursprungliga meddelandet. I praktiken Kontrollera en tjänst att den kan bearbeta feedback innan den upphör. Maximal förfallotiden två dagar, vilket lämnar tid att hämta tjänsten körs igen om ett fel inträffar.

Enligt beskrivningen i [slutpunkter][lnk-endpoints], IoT-hubb ger feedback via en slutpunkt för service-riktade (**/messages/servicebound/feedback**) som meddelanden. Semantiken för att ta emot feedback är desamma som för moln till enhet meddelanden. När det är möjligt batchar meddelandet feedback i ett enda meddelande med följande format:

| Egenskap     | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | Tidsstämpel som anger när feedback-meddelande togs emot av hubben. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Brödtext är en JSON-serialiserad matris med poster, med följande egenskaper:

| Egenskap           | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Tidsstämpel som anger när resultatet av meddelandet har hänt. Till exempel hubben fick meddelandet feedback eller det ursprungliga meddelandet har upphört att gälla. |
| OriginalMessageId  | **MessageId** postmeddelandets moln till enhet som informationen feedback avser. |
| StatusCode         | Strängen som krävs. Används i feedback-meddelanden som genereras av IoT-hubb. <br/> ”Lyckades” <br/> 'Har upphört att gälla: <br/> 'DeliveryCountExceeded' <br/> 'Avvisade' <br/> 'Rensas' |
| Beskrivning        | Sträng som värden för **StatusCode**. |
| DeviceId           | **DeviceId** av målenhet postmeddelandets moln till enhet som denna typ av feedback avser. |
| DeviceGenerationId | **DeviceGenerationId** av målenhet postmeddelandets moln till enhet som denna typ av feedback avser. |

Tjänsten måste ange en **MessageId** för moln till enhet meddelandet för att kunna korrelera dess feedback med det ursprungliga meddelandet.

I följande exempel visas en feedback-postmeddelandets brödtext.

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

## <a name="cloud-to-device-configuration-options"></a>Konfigurationsalternativ för moln till enhet

Varje IoT-hubb visar följande konfigurationsalternativ för meddelanden moln till enhet:

| Egenskap                  | Beskrivning | Intervall och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för moln till enhet meddelanden. | ISO_8601 intervall upp till 2D (minst 1 minut). Standard: 1 timme. |
| MaxDeliveryCount          | Leverans av maximalt antal för köer moln till enhet per enhet. | 1 och 100. Standard: 10. |
| feedback.ttlAsIso8601     | Kvarhållning för service-bunden feedback meddelanden. | ISO_8601 intervall upp till 2D (minst 1 minut). Standard: 1 timme. |
| feedback.maxDeliveryCount |Leverans av maximalt antal för feedbackkö. | 1 och 100. Standard: 100. |

Mer information om hur du anger dessa konfigurationsalternativ finns [skapa IoT-hubbar][lnk-portal].

## <a name="next-steps"></a>Nästa steg

Information om SDK: erna som du kan använda för att ta emot meddelanden moln till enhet finns [Azure IoT SDK][lnk-sdks].

Om du vill prova ta emot meddelanden moln till enhet finns i [skicka moln till enhet] [ lnk-c2d-tutorial] kursen.

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
