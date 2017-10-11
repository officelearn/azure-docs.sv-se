---
title: "Förstå Azure IoT Hub-meddelandeformat | Microsoft Docs"
description: "Utvecklarhandbok - beskrivs formatet och förväntade innehållet i IoT-hubb meddelanden."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.openlocfilehash: e6eafb1a0030b022da2b5d0b787e092f3067c99f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT-hubb

För att stödja smidig samverkan över protokoll, definierar IoT-hubb ett gemensamt meddelandeformat för alla enheter riktade protokoll. Formatet som används för både [enhet till moln] [ lnk-d2c] och [moln till enhet] [ lnk-c2d] meddelanden. En [IoT-hubb meddelandet] [ lnk-messaging] består av:

* En uppsättning *Systemegenskaper*. Egenskaper som IoT-hubb tolkar eller anger. Den här uppsättningen är förinställda.
* En uppsättning *programegenskaper*. En ordlista med egenskaper för anslutningssträngar som programmet kan definiera och åtkomst utan att avbryta serialiseringen för brödtext. IoT-hubb ändrar aldrig dessa egenskaper.
* En täckande binära brödtext.

Egenskapsnamn och värden kan endast innehålla alfanumeriska ASCII-tecken, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` när du:

* Skicka meddelanden från enhet till moln med hjälp av HTTP-protokollet.
* Skicka meddelanden moln till enhet.

Mer information om hur du kodar och avkodar meddelanden som skickas med olika protokoll finns [Azure IoT SDK][lnk-sdks].

I följande tabell visas en uppsättning egenskaper i IoT-hubb-meddelanden.

| Egenskap | Beskrivning |
| --- | --- |
| messageId |En användare går identifierare för meddelandet som används för request-reply-mönster. Format: En skiftlägeskänslig sträng (upp till 128 tecken) av alfanumeriska tecken, ASCII-7-bitars + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Sekvensnummer |En siffra (unika per enhet kö) IoT-hubben har tilldelats varje moln till enhet-meddelande. |
| Till |Ett mål som anges i [moln till enhet] [ lnk-c2d] meddelanden. |
| ExpiryTimeUtc |Datum och tid för meddelandet upphör att gälla. |
| EnqueuedTime |Datum och tid i [moln till enhet] [ lnk-c2d] meddelande togs emot av IoT-hubb. |
| CorrelationId |En strängegenskap i ett svarsmeddelande som vanligtvis innehåller MessageId för begäran i request-reply-mönster. |
| Användar-ID |Ett ID som används för att ange ursprung av meddelanden. När meddelanden som genereras av IoT-hubb, den är inställd på `{iot hub name}`. |
| Ack |En feedback generatorn för meddelandet. Den här egenskapen används i meddelanden moln till enhet för att begära IoT-hubb för att generera feedback meddelanden till följd av användningen av meddelandet av enheten. Möjliga värden: **ingen** (standard): ingen feedback-meddelandet genereras **positivt**: ett meddelande feedback om meddelandet slutfördes **negativa**: ta emot en feedback meddelande om meddelandet har upphört att gälla (eller leverans av maximalt antal nåddes) utan slutförs av enheten, eller **fullständig**: både positiva och negativa. Mer information finns i [meddelande feedback][lnk-feedback]. |
| ConnectionDeviceId |Ett ID som angetts av IoT-hubb på meddelanden från enhet till moln. Den innehåller den **deviceId** på den enhet som skickade meddelandet. |
| ConnectionDeviceGenerationId |Ett ID som angetts av IoT-hubb på meddelanden från enhet till moln. Den innehåller den **generationId** (enligt [identitet enhetsegenskaper][lnk-device-properties]) på den enhet som skickade meddelandet. |
| ConnectionAuthMethod |En autentiseringsmetod som angetts av IoT-hubb på meddelanden från enhet till moln. Den här egenskapen innehåller information om autentiseringsmetoden som används för att autentisera enheten skickar meddelandet. Mer information finns i [enheten till molnet skydd mot förfalskning][lnk-antispoofing]. |

## <a name="message-size"></a>Meddelandestorlek

IoT-hubb mäter meddelandestorlek på ett sätt som protokoll-oberoende överväger faktiska nyttolasten. Storlek i byte beräknas som summan av följande:

* Brödtext storlek i byte.
* Storlek i byte för alla värden i meddelandet Systemegenskaper.
* Storlek i byte för alla användare egenskapsnamn och värden.

Egenskapsnamn och värden är begränsade till ASCII-tecken, så att längden på strängarna som är lika med storleken i byte.

## <a name="next-steps"></a>Nästa steg

Information om storleksgränser för meddelanden i IoT-hubb finns [IoT-hubb kvoter och begränsning][lnk-quotas].

Information om hur du skapar och läsa meddelanden i olika programmeringsspråk för IoT-hubb finns i [Kom igång] [ lnk-get-started] självstudier.

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
