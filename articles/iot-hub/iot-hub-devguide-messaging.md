---
title: Förstå Azure IoT Hub-meddelanden | Microsoft Docs
description: Utvecklarguide – enhet till molnet och molnet till enheten med IoT Hub-meddelanden. Innehåller information om meddelandeformat och stöds kommunikationsprotokoll.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 1915c4bc6cd611479c7575179d8fe64def8895eb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956394"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Skicka meddelanden från enhet till moln och från moln till enhet med IoT Hub

IoT Hub tillåter dubbelriktad kommunikation med dina enheter. Använd IoT Hub-meddelanden för att kommunicera med dina enheter genom att skicka meddelanden från dina enheter till serverdelen för lösningar och skicka kommandon från den dina IoT-lösningar serverdel till dina enheter. Läs mer om den [IoT Hub-meddelandeformat](../iot-hub/iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Skicka meddelanden från enhet till moln till IoT Hub

IoT Hub har en inbyggd slutpunkt som kan användas av backend-tjänster kan läsa telemetrimeddelanden från från dina enheter. Den här slutpunkten är kompatibel med [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/) och du kan använda standard IoT Hub SDK: er till [läsa från den här inbyggda slutpunkten]((https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)).

IoT Hub stöder också [anpassade slutpunkter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints) som kan definieras av användare att skicka enhetsdata för telemetri och händelser till Azure-tjänster med [meddelanderoutning](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Skicka meddelanden från moln till enhet från IoT Hub

Du kan skicka [moln till enhet](iot-hub-devguide-messages-c2d.md) meddelanden från lösningen serverdel till dina enheter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Core-egenskaperna för IoT Hub meddelandefunktioner är tillförlitlighet och hållbarhet av meddelanden. De här egenskaperna aktivera återhämtning till tillfällig anslutning på enheten och att läsa in toppar i händelsebearbetning på molnet sida. IoT Hub implementerar *minst en gång* leverans garanterar för både enheten till molnet och moln-till-enhet-meddelanden.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Välja rätt typ av IoT Hub-meddelanden

Använda meddelanden från enheten till molnet för att skicka time series telemetri och aviseringar från appen för enheter och meddelanden från moln till enhet för enkelriktade meddelanden till din enhetsapp.

* Referera till [enhet till molnet kommunikation vägledning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) att välja bland meddelanden från enheten till molnet, rapporterade egenskaper eller filöverföring.
* Referera till [moln till enhet kommunikation vägledning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) att välja mellan meddelanden från moln till enhet, önskade egenskaper eller direkta metoder.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om IoT Hub [meddelanderoutning](iot-hub-devguide-messages-d2c.md).
* Lär dig mer om IoT Hub [moln till enhet messaging](iot-hub-devguide-messages-c2d.md).