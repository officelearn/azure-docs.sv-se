---
title: Inträngning för enhetsanslutning och telemetri – Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du ansluter, ar ombord och skickar telemetri från en IoT-enhet i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862483"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Enhetsanslutning och inkommande telemetri

Telemetridata som skickas av enheter och sensorer utgör ryggraden i alla IoT-lösningar. Hur man representerar dessa olika resurser och hantera dem inom ramen för en plats är främsta oro i IoT app utveckling. Azure Digital Twins förenklar processen att utveckla IoT-lösningar genom att förena enheter och sensorer med en spatial intelligens graf.

För att komma igång, skapa en Azure IoT Hub-resurs i roten av det rumsliga diagrammet. IoT Hub-resursen gör att alla enheter under rotutrymmet kan skicka meddelanden. När IoT Hub har skapats registrerar du enheter med sensorer i Digital Twins-instansen. Enheterna kan skicka data till en Digital Twins-tjänst via [Azure IoT-enheten SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

En steg-för-steg-guide om hur du tar med enheter ombord läser [du självstudien för att distribuera och konfigurera Digitala tvillingar](tutorial-facilities-setup.md). I korthet är stegen:

- Distribuera en Digital Twins-instans från [Azure-portalen](https://portal.azure.com).
- Skapa blanksteg i diagrammet.
- Skapa en IoT Hub-resurs och tilldela den till ett utrymme i diagrammet.
- Skapa enheter och sensorer i diagrammet och tilldela dem till de utrymmen som skapats i föregående steg.
- Skapa en matchning för att filtrera telemetrimeddelanden baserat på villkor.
- Skapa en [användardefinierad funktion](concepts-user-defined-functions.md)och tilldela den till ett utrymme i diagrammet för anpassad bearbetning av telemetrimeddelanden.
- Tilldela en roll så att den användardefinierade funktionen kan komma åt diagramdata.
- Hämta anslutningssträngen för IoT Hub-enheten från Digital Twins Management API:er.
- Konfigurera enhetens anslutningssträng på enheten med Azure IoT-enheten SDK.

I följande avsnitt får du lära dig hur du hämtar anslutningssträngen för IoT Hub-enheten från Digital Twins Management API. Du lär dig också hur du använder ioT Hub telemetri meddelandeformat för att skicka sensorbaserad telemetri. Digital Twins kräver varje bit telemetri som den tar emot för att associeras med en sensor i den rumsliga grafen. Detta krav säkerställer att data bearbetas och dirigeras inom lämplig rumslig kontext.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Hämta anslutningssträngen för IoT Hub-enheten från hanterings-API:et

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Gör ett GET-anrop på `includes=ConnectionString` enhets-API:et med en parameter för att hämta anslutningssträngen för IoT Hub-enheten. Filtrera efter enhetens GUID eller maskinvaru-ID för att hitta den angivna enheten.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_GUID* | Enhets-ID:et |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parametervärde | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Enhetens maskinvaru-ID |

Kopiera enhetens **connectionString-egenskap** i svarsnyttolasten. Du använder den när du anropar Azure IoT-enheten SDK för att skicka data till Digital Twins.

## <a name="device-to-cloud-message"></a>Meddelande från enhet till moln

Du kan anpassa enhetens meddelandeformat och nyttolast så att de passar din lösnings behov. Använd alla datakontrakt som kan serialiseras till en byte-matris eller ström som stöds av [klassen Azure IoT Device Client Message, Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Meddelandet kan vara ett anpassat binärt format som du väljer, så länge du avkodar datakontraktet i en motsvarande användardefinierad funktion. Det finns bara ett krav för ett meddelande från enhet till moln. Underhåll en uppsättning egenskaper för att se till att meddelandet dirigeras på rätt sätt till bearbetningsmotorn.

### <a name="telemetry-properties"></a>Egenskaper för telemetri

 Nyttolastens innehåll i ett **meddelande** kan vara godtyckliga data upp till 256 kB i storlek. Det finns några krav som [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) förväntas för egenskaper av typen. Tabellen visar de obligatoriska och valfria egenskaper som stöds av systemet.

| Egenskapsnamn | Värde | Krävs | Beskrivning |
|---|---|---|---|
| **DigitalTwins-telemetri** | 1.0 | Ja | Ett konstant värde som identifierar ett meddelande till systemet. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ja | En unik identifierare för sensorn som skickar **meddelandet**. Det här värdet måste matcha egenskapen **HardwareId** för att systemet ska kunna bearbetas. Till exempel `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Inga | En [ISO 8601-formaterad](https://www.iso.org/iso-8601-date-and-time-format.html) datumsträng som identifierar samplingstiden för nyttolasten. Till exempel `2018-09-20T07:35:00.8587882-07:00`. |
| **KorrelationId** | `string` | Inga | En UUID som används för att spåra händelser i hela systemet. Till exempel `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Skicka ditt meddelande till Digital Twins

Använd callen DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) eller [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) för att skicka meddelandet till Digital Twins.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure Digital Twins databehandling och användardefinierade funktioner funktioner funktioner, läsa [Azure Digital Twins databehandling och användardefinierade funktioner](concepts-user-defined-functions.md).
