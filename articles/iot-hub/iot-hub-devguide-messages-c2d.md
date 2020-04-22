---
title: Förstå Azure IoT Hub-meddelanden från molnet till enheten | Microsoft-dokument
description: I den här utvecklarguiden beskrivs hur du använder meddelanden från molnet till enheten med din IoT-hubb. Den innehåller information om meddelandets livscykel och konfigurationsalternativ.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729998"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Skicka meddelanden från molnet till enheten från en IoT-hubb

Om du vill skicka enkelriktade meddelanden till en enhetsapp från lösningens serverdel skickar du meddelanden från molnet till enheten från IoT-hubben till enheten. En diskussion om andra moln-till-enhet-alternativ som stöds av Azure IoT Hub finns i [kommunikationsvägledning från molnet till enhet](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Du skickar meddelanden från molnet till enheten via en tjänstinriktad slutpunkt, */messages/devicebound*. En enhet tar sedan emot meddelandena via en enhetsspecifik slutpunkt, */devices/{deviceId}/messages/devicebound*.

Om du vill rikta in dig på varje meddelande från molnet till enheten på en enda enhet anger IoT-hubben **egenskapen till** */devices/{deviceId}/messages/devicebound*.

Varje enhetskö innehåller högst 50 meddelanden från molnet till enheten. Om du vill försöka skicka fler meddelanden till samma enhet uppstår ett fel.

## <a name="the-cloud-to-device-message-life-cycle"></a>Livscykeln för molnet-till-enhet-meddelande

För att garantera minst en gång meddelandeleverans, din IoT-hubb beständiga moln-till-enhet-meddelanden i per enhet köer. För att IoT-hubben ska ta bort meddelandena från kön måste enheterna uttryckligen bekräfta *slutförande*. Den här metoden garanterar återhämtning mot anslutning och enhetsfel.

Livscykeltillståndsdiagrammet visas i följande diagram:

![Livscykel för moln-till-enhet-meddelande](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

När IoT-hubbtjänsten skickar ett meddelande till en enhet anger tjänsten meddelandetillståndet till *Enqueued*. När en enhet vill *ta emot* ett meddelande *låser* IoT-hubben meddelandet genom att ange tillståndet till *Osynligt*. Med det här tillståndet kan andra trådar på enheten börja ta emot andra meddelanden. När en enhetstråd är klar med bearbetningen av ett meddelande meddelar den IoT-hubben genom *att slutföra* meddelandet. IoT-hubben anger sedan tillståndet till *Slutförd*.

En enhet kan också:

* *Avvisa* meddelandet, vilket gör att IoT-hubben ställer in det på tillståndet *Död bokstav.* Enheter som ansluter via MQTT-protokollet (Message Queuing Telemetry Transport) kan inte avvisa meddelanden från molnet till enheten.

* *Överge* meddelandet, vilket gör att IoT-hubben för att skicka tillbaka meddelandet i kön, med tillståndet inställt på *Enqueued*. Enheter som ansluter via MQTT-protokollet kan inte överge meddelanden från molnet till enheten.

En tråd kan misslyckas med att bearbeta ett meddelande utan att meddela IoT-hubben. I det här fallet övergår meddelanden automatiskt från det *osynliga* tillståndet tillbaka till tillståndet *Enqueued* efter en *tidsförd time-out* (eller *låser* time-out). Värdet för den här time-outen är en minut och kan inte ändras.

Egenskapen **max leveransantal** på IoT-hubben bestämmer det maximala antalet gånger ett meddelande kan övergå mellan tillstånden *Enqueued* och *Invisible.* Efter det antalet övergångar anger IoT-hubben meddelandets tillstånd till *Död bokstäver*. På samma sätt anger IoT-hubben tillståndet för ett meddelande till *Död bokstäver* efter dess utgångstid. Mer information finns i [Tid att leva](#message-expiration-time-to-live).

Artikeln [Hur du skickar meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) visar hur du skickar meddelanden från molnet till enheten och ta emot dem på en enhet.

En enhet slutför normalt ett meddelande från molnet till enheten när förlusten av meddelandet inte påverkar programlogiken. Ett exempel på detta kan vara när enheten har sparat meddelandeinnehållet lokalt eller har utfört en åtgärd. Meddelandet kan också innehålla tillfällig information, vars förlust inte skulle påverka programmets funktionalitet. Ibland kan du för långvariga uppgifter:

* Slutför meddelandet från molnet till enheten när enheten har sparat uppgiftsbeskrivningen i lokal lagring.

* Meddela lösningens serverdel med ett eller flera meddelanden från enhet till moln i olika stadier av aktivitetens förlopp.

## <a name="message-expiration-time-to-live"></a>Meddelandets förfallodatum (tid att leva)

Varje meddelande från molnet till enheten har en förfallotid. Den här gången ställs in av något av följande:

* Egenskapen **ExpiryTimeUtc** i tjänsten
* IoT-hubben genom att använda *standardtiden för* att live som anges som en IoT-hubbegenskap

Se [Konfigurationsalternativ för molnet till enhet](#cloud-to-device-configuration-options).

Ett vanligt sätt att dra nytta av ett meddelande förfallodatum och undvika att skicka meddelanden till frånkopplade enheter är att ställa in kort *tid till live-värden.* Den här metoden uppnår samma resultat som att upprätthålla enhetens anslutningstillstånd, men det är effektivare. När du begär meddelandebekräftelser meddelar IoT-hubben vilka enheter som är:

* Kan ta emot meddelanden.
* Är inte online eller har misslyckats.

## <a name="message-feedback"></a>Feedback från meddelanden

När du skickar ett meddelande från molnet till enheten kan tjänsten begära leverans av feedback per meddelande om det slutliga tillståndet för meddelandet. Du gör detta genom att ange egenskapen **iothub-ack-program** i meddelandet från molnet till enheten som skickas till ett av följande fyra värden:

| Egenskapsvärde för Ack | Beteende |
| ------------ | -------- |
| ingen     | IoT-hubben genererar inget feedbackmeddelande (standardbeteende). |
| positivt | Om molnet-till-enhet-meddelandet når tillståndet *Slutförd* genererar IoT-hubben ett feedbackmeddelande. |
| negativt | Om molnet-till-enhet-meddelandet når tillståndet *Död bokstäver* genererar IoT-hubben ett feedbackmeddelande. |
| fullständig     | IoT-hubben genererar ett feedbackmeddelande i båda fallen. |

Om **Ack-värdet** är *fullt*och du inte får något feedbackmeddelande betyder det att feedbackmeddelandet har upphört att gälla. Tjänsten kan inte veta vad som hände med det ursprungliga meddelandet. I praktiken bör en tjänst se till att den kan bearbeta feedbacken innan den löper ut. Den maximala förfallotiden är två dagar, vilket ger tid att få tjänsten igång igen om ett fel inträffar.

Som förklaras i [slutpunkter](iot-hub-devguide-endpoints.md)levererar IoT-hubben feedback via en tjänstinriktad slutpunkt, */messages/servicebound/feedback*, som meddelanden. Semantiken för att ta emot feedback är samma som för meddelanden från molnet till enheten. När det är möjligt batchas meddelandefeedback i ett enda meddelande med följande format:

| Egenskap     | Beskrivning |
| ------------ | ----------- |
| EnqueuedTime | En tidsstämpel som anger när feedbackmeddelandet togs emot av navet |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

Brödtexten är en JSON-serialiserad matris med poster, var och en med följande egenskaper:

| Egenskap           | Beskrivning |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | En tidsstämpel som anger när resultatet av meddelandet inträffade (till exempel tog navet emot feedbackmeddelandet eller det ursprungliga meddelandet upphörde att gälla) |
| OriginalMessageId  | *MessageId* för molnet-till-enhet-meddelandet som den här feedbackinformationen gäller |
| Statuskod         | En obligatorisk sträng som används i feedbackmeddelanden som genereras av IoT-hubben: <br/> *Framgång* <br/> *Upphörd* <br/> *DeliveryCountExceeded* <br/> *Avvisad* <br/> *Renade* |
| Beskrivning        | Strängvärden för *StatusCode* |
| DeviceId           | *DeviceId* för målenheten för det meddelande från molnet till enheten som den här feedbacken gäller |
| DeviceGenerationId | *DeviceGenerationId* för målenheten för molnet-till-enhet-meddelandet som den här feedbacken gäller |

För att meddelandet mellan molnet och enheten ska korrelera feedbacken med det ursprungliga meddelandet måste tjänsten ange en *MessageId*.

Brödtexten i ett feedbackmeddelande visas i följande kod:

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

**Väntande feedback för borttagna enheter**

När en enhet tas bort tas även väntande feedback bort. Feedback från enheten skickas i omgångar. Om en enhet tas bort i det smala fönstret (ofta mindre än 1 sekund) mellan när enheten bekräftar mottagandet av meddelandet och när nästa feedbackbatch förbereds, kommer feedbacken inte att ske.

Du kan åtgärda detta genom att vänta en viss tid på att väntande feedback ska komma fram innan du tar bort enheten. Feedback från relaterade meddelanden bör antas gå förlorad när en enhet har tagits bort.

## <a name="cloud-to-device-configuration-options"></a>Konfigurationsalternativ för molnet till enhet

Varje IoT-hubb visar följande konfigurationsalternativ för meddelanden från molnet till enheten:

| Egenskap                  | Beskrivning | Intervall och standard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standard-TTL för meddelanden från molnet till enheten | ISO_8601 intervall upp till 2 dagar (minst 1 minut); standard: 1 timme |
| maxDeliveryCount          | Maximalt antal leveranser för köer mellan molnet och enheten per enhet | 1 till 100. standard: 10 |
| feedback.ttlAsIso8601     | Kvarhållning för tjänstbundna feedbackmeddelanden | ISO_8601 intervall upp till 2 dagar (minst 1 minut); standard: 1 timme |
| feedback.maxDeliveryCount | Maximalt leveransantal för feedbackkön | 1 till 100. standard: 10 |
| feedback.lockDurationAsIso8601 | Maximalt leveransantal för feedbackkön | ISO_8601 intervall från 5 till 300 sekunder (minst 5 sekunder); standard: 60 sekunder. |

Du kan ange konfigurationsalternativen på något av följande sätt:

* **Azure-portal:** Under **Inställningar** på din IoT-hubb väljer du **Inbyggda slutpunkter** och expanderar Cloud till **enhetsmeddelanden**. (Ange egenskaperna **feedback.maxDeliveryCount** och **feedback.lockDurationAsIso8601** stöds för närvarande inte i Azure-portalen.)

    ![Ange konfigurationsalternativ för meddelanden från molnet till enheten i portalen](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Använd kommandot [az iot hub update:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Nästa steg

Information om de SDK:er som du kan använda för att ta emot meddelanden från molnet till enheten finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

Information om hur du tar emot meddelanden från molnet till enheten finns i självstudien [Skicka molnet till enheten.](iot-hub-csharp-csharp-c2d.md)
