---
title: Azure IoT Hub moln-till-enhet alternativ | Microsoft-dokument
description: Utvecklarguide – vägledning om när du ska använda direkta metoder, enhetstvillingens önskade egenskaper eller meddelanden från molnet till enheten för kommunikation mellan molnet och enheten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b5682334bd3fb23fbbebed5fc8ece6d55e9c5652
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733229"
---
# <a name="cloud-to-device-communications-guidance"></a>Kommunikationsvägledning för molnet till enheten

IoT Hub innehåller tre alternativ för enhetsappar för att exponera funktioner för en backend-app:

* [Direkta metoder](iot-hub-devguide-direct-methods.md) för kommunikation som kräver omedelbar bekräftelse av resultatet. Direkta metoder används ofta för interaktiv styrning av enheter som att slå på en fläkt.

* [Twins önskade egenskaper](iot-hub-devguide-device-twins.md) för långvariga kommandon avsedda att sätta enheten i ett visst önskat tillstånd. Ange till exempel telemetrissändintervallet till 30 minuter.

* [Meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md) för enkelriktade meddelanden till enhetsappen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Här är en detaljerad jämförelse av de olika moln-till-enhet kommunikationsalternativ.

|  | Direkta metoder | Twins önskade egenskaper | Meddelanden från molnet till enheten |
| ---- | ------- | ---------- | ---- |
| Scenario | Kommandon som kräver omedelbar bekräftelse, till exempel att slå på en fläkt. | Långvariga kommandon avsedda att sätta enheten i ett visst önskat tillstånd. Ange till exempel telemetrissändintervallet till 30 minuter. | Enkelriktade meddelanden till enhetsappen. |
| Dataflöde | Dubbelriktad. Enhetsappen kan svara på metoden direkt. Lösningens baksida tar emot resultatet kontextuellt till begäran. | Enkelriktad. Enhetsappen får ett meddelande med egenskapsändringen. | Enkelriktad. Enhetsappen tar emot meddelandet
| Hållbarhet | Frånkopplade enheter kontaktas inte. Lösningens baksida meddelas om att enheten inte är ansluten. | Egenskapsvärden bevaras i enhetstvillingen. Enheten kommer att läsa den vid nästa återanslutning. Egenskapsvärden kan hämtas med [frågespråket IoT Hub](iot-hub-devguide-query-language.md). | Meddelanden kan behållas av IoT Hub i upp till 48 timmar. |
| Mål | En enhet som använder **deviceId**eller flera enheter med [jobb](iot-hub-devguide-jobs.md). | En enhet som använder **deviceId**eller flera enheter med [jobb](iot-hub-devguide-jobs.md). | En enhet per **deviceId**. |
| Storlek | Maximal direkt metod nyttolaststorlek är 128 KB. | Maximal önskad egenskapsstorlek är 32 KB. | Upp till 64 KB-meddelanden. |
| Frequency | Hög. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). | Medel. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub-gränser](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Tillgänglig med MQTT eller AMQP. | Tillgänglig med MQTT eller AMQP. | Finns på alla protokoll. Enheten måste avsöka när du använder HTTPS. |

Lär dig hur du använder direkta metoder, önskade egenskaper och meddelanden från molnet till enheten i följande självstudier:

* [Använda direkta metoder](quickstart-control-device-node.md)
* [Använda önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) 
* [Skicka meddelanden från moln till enhet](iot-hub-node-node-c2d.md)
