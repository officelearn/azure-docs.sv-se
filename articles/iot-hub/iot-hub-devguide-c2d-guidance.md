---
title: Alternativ för Azure IoT Hub-moln till enhet | Microsoft Docs
description: Developer guide – riktlinjer för när du ska använda direkta metoder, enhetstvillingens önskade egenskaper eller meddelanden från moln till enhet för meddelanden från moln till enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 4b738f34ae75478c0120832e7ad2b6a6a83dbf69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224786"
---
# <a name="cloud-to-device-communications-guidance"></a>Moln-till-enhet-kommunikation

IoT Hub tillhandahåller tre alternativ för enhetsappar att exponera funktioner till en backend-app:

* [Direkta metoder](iot-hub-devguide-direct-methods.md) för kommunikation som kräver omedelbar bekräftelse på resultatet. Direkta metoder används ofta för interaktiva kontroll över enheter, till exempel aktivera en fläkt.

* [Önskade egenskaper för Twin](iot-hub-devguide-device-twins.md) för tidskrävande kommandon som är avsedd att placera enheten i ett visst önskat tillstånd. Till exempel skicka telemetriintervall till 30 minuter.

* [Meddelanden från moln till enhet](iot-hub-devguide-messages-c2d.md) för enkelriktade meddelanden i enhetsappen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Här är en detaljerad jämförelse av de olika alternativ för kommunikation från moln till enhet.

|  | Direkta metoder | Önskad tvillingegenskaper | Meddelanden från moln till enhet |
| ---- | ------- | ---------- | ---- |
| Scenario | Kommandon som kräver omedelbar bekräftelse, till exempel aktivera en fläkt. | Långvariga kommandon ska placera enheten i ett visst önskat tillstånd. Till exempel skicka telemetriintervall till 30 minuter. | Enkelriktade meddelanden i enhetsappen. |
| Dataflöde | Dubbelriktad. Enhetsappen kan svara på metoden direkt. Lösningens backend-server tar emot resultatet sammanhangsmässigt att begäran. | Enkelriktad. App för enheter tar emot ett meddelande med egenskapsändringen. | Enkelriktad. App för enheter tar emot meddelandet
| Hållbarhet | Frånkopplade enheter kontaktas inte. Lösningens backend-server är ett meddelande om att enheten inte är ansluten. | Egenskapsvärden bevaras i enhetstvillingen. Enheten kommer att läsa det vid nästa återanslutning. Egenskapsvärden är hämtningsbara med den [IoT Hub-frågespråk](iot-hub-devguide-query-language.md). | Meddelanden sparas av IoT Hub i upp till 48 timmar. |
| Mål | Enskild enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb](iot-hub-devguide-jobs.md). | Enskild enhet med hjälp av **deviceId**, eller flera enheter med hjälp av [jobb](iot-hub-devguide-jobs.md). | Enskild enhet genom att **deviceId**. |
| Storlek | Maximal direkt metod Nyttolaststorlek är 128 KB. | Maximalt önskade egenskaper är 8 KB. | Upp till 64 KB-meddelanden. |
| Frekvens | Hög. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). | Medel. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub begränsar](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Tillgängligt med hjälp av MQTT eller AMQP. | Tillgängligt med hjälp av MQTT eller AMQP. | Tillgänglig på alla protokoll. Enheten måste avsöka när du använder HTTPS. |

Lär dig hur du använder direkta metoder, egenskaper och meddelanden från molnet till enheten i följande Självstudier:

* [Använd direkta metoder](quickstart-control-device-node.md)
* [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) 
* [Skicka meddelanden från moln till enhet](iot-hub-node-node-c2d.md)