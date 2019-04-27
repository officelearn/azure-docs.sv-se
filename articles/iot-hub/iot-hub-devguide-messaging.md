---
title: Förstå Azure IoT Hub-meddelanden | Microsoft Docs
description: Utvecklarguide – enhet till molnet och molnet till enheten med IoT Hub-meddelanden. Innehåller information om meddelandeformat och stöds kommunikationsprotokoll.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626255"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Skicka meddelanden från enhet till moln och från moln till enhet med IoT Hub

IoT Hub tillåter dubbelriktad kommunikation med dina enheter. Använd IoT Hub-meddelanden för att kommunicera med dina enheter genom att skicka meddelanden från dina enheter till dina lösningar tillbaka Avsluta och skicka kommandon från dina IoT lösningar serverdel till dina enheter. Läs mer om den [IoT Hub-meddelandeformat](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Skicka meddelanden från enhet till moln till IoT Hub

IoT Hub har en inbyggd slutpunkt som kan användas av backend-tjänster kan läsa telemetrimeddelanden från från dina enheter. Den här slutpunkten är kompatibel med [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/) och du kan använda standard IoT Hub SDK: er till [läsa från den här inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md).

IoT Hub stöder också [anpassade slutpunkter](iot-hub-devguide-endpoints.md#custom-endpoints) som kan definieras av användare att skicka enhetsdata för telemetri och händelser till Azure-tjänster med [meddelanderoutning](iot-hub-devguide-messages-d2c.md).

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