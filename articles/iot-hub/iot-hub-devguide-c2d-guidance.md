---
title: Alternativ för Azure IoT Hub-moln till enhet | Microsoft Docs
description: Developer guide – riktlinjer för när du ska använda direkta metoder, enhetstvillingens önskade egenskaper eller meddelanden från moln till enhet för meddelanden från moln till enhet.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187044"
---
# <a name="cloud-to-device-communications-guidance"></a>Moln-till-enhet-kommunikation
IoT Hub tillhandahåller tre alternativ för enhetsappar att exponera funktioner till en backend-app:

* [Direkta metoder] [ lnk-methods] för kommunikation som kräver omedelbar bekräftelse på resultatet. Direkta metoder används ofta för interaktiva kontroll över enheter, till exempel aktivera en fläkt.
* [Önskade egenskaper för Twin] [ lnk-twins] för tidskrävande kommandon som är avsedd att placera enheten i ett visst önskat tillstånd. Till exempel skicka telemetriintervall till 30 minuter.
* [Meddelanden från moln till enhet] [ lnk-c2d] för enkelriktade meddelanden i enhetsappen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Här är en detaljerad jämförelse av de olika alternativ för kommunikation från moln till enhet.

|  | Direkta metoder | Önskad tvillingegenskaper | Meddelanden från moln till enhet |
| ---- | ------- | ---------- | ---- |
| Scenario | Kommandon som kräver omedelbar bekräftelse, till exempel aktivera en fläkt. | Långvariga kommandon ska placera enheten i ett visst önskat tillstånd. Till exempel skicka telemetriintervall till 30 minuter. | Enkelriktade meddelanden i enhetsappen. |
| Dataflöde | Dubbelriktad. Enhetsappen kan svara på metoden direkt. Lösningens backend-server tar emot resultatet sammanhangsmässigt att begäran. | Enkelriktad. App för enheter tar emot ett meddelande med egenskapsändringen. | Enkelriktad. App för enheter tar emot meddelandet
| Hållbarhet | Frånkopplade enheter kontaktas inte. Lösningens backend-server är ett meddelande om att enheten inte är ansluten. | Egenskapsvärden bevaras i enhetstvillingen. Enheten kommer att läsa det vid nästa återanslutning. Egenskapsvärden är hämtningsbara med den [IoT Hub-frågespråk][lnk-query]. | Meddelanden sparas av IoT Hub i upp till 48 timmar. |
| Mål | Enskild enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb][lnk-jobs]. | Enskild enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb][lnk-jobs]. | Enskild enhet genom att **deviceId**. |
| Storlek | Maximal direkt metod Nyttolaststorlek är 128 KB. | Maximalt önskade egenskaper är 8 KB. | Upp till 64 KB-meddelanden. |
| Frekvens | Hög. Mer information finns i [IoT Hub begränsar][lnk-quotas]. | Medel. Mer information finns i [IoT Hub begränsar][lnk-quotas]. | Låg. Mer information finns i [IoT Hub begränsar][lnk-quotas]. |
| Protokoll | Tillgängligt med hjälp av MQTT eller AMQP. | Tillgängligt med hjälp av MQTT eller AMQP. | Tillgänglig på alla protokoll. Enheten måste avsöka när du använder HTTPS. |

Lär dig hur du använder direkta metoder, egenskaper och meddelanden från molnet till enheten i följande Självstudier:

* [Använd direkta metoder][lnk-methods-tutorial], för direkta metoder;
* [Använd önskade egenskaper för att konfigurera enheter][lnk-twin-properties]för enhetstvilling önskade egenskaper. 
* [Skicka meddelanden från moln till enhet][lnk-c2d-tutorial], för meddelanden från molnet till enheten.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
