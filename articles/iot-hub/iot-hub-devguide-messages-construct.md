---
title: Förstå Azure IoT Hub-meddelandeformat | Microsoft Docs
description: Utvecklarguide – beskrivs formatet och förväntade innehållet i meddelanden från IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286525"
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT Hub-meddelanden

Om du vill ha stöd för smidig samverkan mellan protokoll, definierar IoT Hub ett vanliga meddelandeformat för alla protokoll för webbservergrupper på enheten. Meddelanden i formatet används för både [enhet till moln] [ lnk-d2c] och [moln till enhet] [ lnk-c2d] meddelanden. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

En [IoT Hub-meddelande] [ lnk-messaging] består av:

* En förutbestämd uppsättning *Systemegenskaper* enligt nedan.
* En uppsättning *programegenskaper*. En ordlista med egenskaper för anslutningssträngar som programmet kan definiera och åtkomst, utan att behöva deserialisera meddelandetexten. IoT Hub ändrar aldrig de här egenskaperna.
* En täckande binära brödtext.

Egenskapsnamn och värden får bara innehålla alfanumeriska ASCII-tecken, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` när du:  

* Skicka meddelanden från enheten till molnet med hjälp av HTTPS-protokollet.
* Skicka meddelanden från molnet till enheten.

Läs mer om hur du kodar och avkodar meddelandena som skickas med olika protokoll, [Azure IoT SDK: er][lnk-sdks].

I följande tabell visar uppsättningen Systemegenskaper i IoT Hub-meddelanden.

| Egenskap  | Beskrivning | Är användare inställbar? |
| --- | --- | --- |
| MessageId |En användare inställbar identifierare för meddelandet som används för begäran / svar-mönster. Format: En skiftlägeskänslig sträng (upp till 128 tecken) med ASCII 7 bitar alfanumeriska tecken + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Ja |
| Sekvensnummer |Ett tal (unika per enhet kö) IoT Hub har tilldelats varje moln-till-enhet-meddelande. | Nej för C2D-meddelanden. Ja annars. |
| Till |Ett mål som anges i [moln till enhet] [ lnk-c2d] meddelanden. | Nej för C2D-meddelanden. Ja annars. |
| ExpiryTimeUtc |Datum och tid för förfallodatum för meddelanden. | Ja |
| EnqueuedTime |Datum och tid i [moln till enhet] [ lnk-c2d] meddelande togs emot av IoT Hub. | Nej för C2D-meddelanden. Ja annars. |
| CorrelationId |En strängegenskap i ett svarsmeddelande som vanligtvis innehåller meddelande-ID för begäran i begäran / svar-mönster. | Ja |
| UserId |Ett ID som används för att ange ursprunget av meddelanden. När meddelanden genereras av IoT Hub, den är inställd på `{iot hub name}`. | Nej |
| Ack |En feedback meddelande generator. Den här egenskapen används i meddelanden från moln till enhet för att begära IoT Hub för att generera meddelanden på grund av användningen av meddelandet av enheten. Möjliga värden: **ingen** (standard): ingen feedback meddelande genereras **positivt**: meddelandet feedback om meddelandet har slutförts, **negativt**: ta emot en feedback-meddelande om meddelandet har gått ut (eller har nått maximalt antal leveranser) utan håller på att slutföras av enheten, eller **fullständig**: både positiva och negativa. Mer information finns i [meddelande feedback][lnk-feedback]. | Ja |
| ConnectionDeviceId |Ett ID som angetts av IoT Hub på meddelanden från enheten till molnet. Den innehåller den **deviceId** på den enhet som skickade meddelandet. | Nej för D2C-meddelanden. Ja annars. |
| ConnectionDeviceGenerationId |Ett ID som angetts av IoT Hub på meddelanden från enheten till molnet. Den innehåller den **generationId** (enligt [identitet enhetsegenskaper][lnk-device-properties]) på den enhet som skickade meddelandet. | Nej för D2C-meddelanden. Ja annars. |
| ConnectionAuthMethod |En autentiseringsmetod som angetts av IoT Hub på meddelanden från enheten till molnet. Den här egenskapen innehåller information om den autentiseringsmetod som används för att autentisera den enhet som skickar meddelandet. Mer information finns i [enhet till moln skydd mot förfalskning][lnk-antispoofing]. | Nej för D2C-meddelanden. Ja annars. |
| CreationTimeUtc | Datum och tidpunkt som meddelandet skapades på en enhet. En enhet måste ange ett explicit värde. | Ja |

## <a name="message-size"></a>Meddelandestorlek

IoT Hub mäter meddelandestorlek på ett protokoll-oberoende sätt, överväger bara den faktiska nyttolasten. Storlek i byte beräknas som summan av följande:

* Brödtext storlek i byte.
* Storlek i byte för alla värden i systemet meddelandeegenskaper.
* Storlek i byte för alla användare egenskapsnamn och värden.

Egenskapsnamn och värden är begränsade till ASCII-tecken så lång strängarna är lika storlek i byte.

## <a name="next-steps"></a>Nästa steg

Information om begränsningar för meddelandestorlek i IoT Hub finns i [IoT Hub-kvoter och begränsningar][lnk-quotas].

Information om hur du skapar och läsa IoT Hub-meddelanden i olika programmeringsspråk, finns det [Snabbstarter][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
