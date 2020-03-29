---
title: Förstå Azure IoT Hub-meddelanden | Microsoft-dokument
description: Utvecklarguide – meddelanden från enhet till moln och från molnet till enheten med IoT Hub. Innehåller information om meddelandeformat och kommunikationsprotokoll som stöds.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626255"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Skicka meddelanden från enhet till moln och från molnet till enheten med IoT Hub

IoT Hub möjliggör dubbelriktad kommunikation med dina enheter. Använd IoT Hub-meddelanden för att kommunicera med dina enheter genom att skicka meddelanden från dina enheter till dina lösningar i serverdelen och skicka kommandon från din IoT-lösningar tillbaka till dina enheter. Läs mer om [meddelandeformatet IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Skicka meddelanden från enhet till moln till IoT Hub

IoT Hub har en inbyggd tjänstslutpunkt som kan användas av backend-tjänster för att läsa telemetrimeddelanden från dina enheter. Den här slutpunkten är kompatibel med [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) och du kan använda standard-IoT Hub-SDK:er för att [läsa från den här inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md).

IoT Hub stöder också [anpassade slutpunkter](iot-hub-devguide-endpoints.md#custom-endpoints) som kan definieras av användare för att skicka enhetstelemetridata och händelser till Azure-tjänster med hjälp av [meddelanderoutning](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Skicka meddelanden från molnet till enheten från IoT Hub

Du kan skicka meddelanden från [molnet till enheten](iot-hub-devguide-messages-c2d.md) från lösningens serverdel till dina enheter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Grundläggande egenskaper för IoT Hub-meddelandefunktioner är meddelandenas tillförlitlighet och hållbarhet. Dessa egenskaper möjliggör återhämtning till intermittent anslutning på enhetssidan och för att läsa in toppar i händelsebearbetning på molnsidan. IoT Hub implementerar *minst en gång* leveransgarantier för både enhet-till-moln- och moln-till-enhet-meddelanden.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Välja rätt typ av IoT Hub-meddelanden

Använd meddelanden från enhet till moln för att skicka telemetri och aviseringar från enhetsappen och meddelanden från molnet till enheten för enkelriktade meddelanden till enhetsappen.

* Se [kommunikationsvägledning för enhet till moln](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) för att välja mellan meddelanden från enhet till moln, rapporterade egenskaper eller filöverföring.

* Se [kommunikationsvägledning från molnet till enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) för att välja mellan meddelanden från molnet till enheten, önskade egenskaper eller direkta metoder.

## <a name="next-steps"></a>Nästa steg

* Läs mer om IoT [Hub-meddelanderoutning](iot-hub-devguide-messages-d2c.md).

* Läs mer om IoT [Hub-meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md).