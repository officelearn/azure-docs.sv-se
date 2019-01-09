---
title: Enhetens anslutning och telemetriingång med Azure Digital Twins | Microsoft Docs
description: Översikt över hur du återställer en enhet komma igång med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e6d95d44dbfe2d66189be5103552d841ccbdf690
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117426"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Enhetsanslutning och inkommande telemetri

Dessa data skickas från enheterna och sensorerna utgör basen för alla IoT-lösningar. Hur representerar dessa olika resurser och hantera dem inom ramen för en plats är chief frågor i IoT-apputveckling. Azure Digital Twins förenklar arbetet med att utveckla IoT-lösningar genom att förena enheter och sensorer med ett spatial intelligence-diagram.

Kom igång genom att skapa en Azure IoT Hub-resurs i roten för spatial diagrammet. IoT Hub-resurs kan alla enheter under rot-utrymme för att skicka meddelanden. När IoT-hubben har skapats kan du registrera enheter med sensorer inom Digital Twins-instansen. Enheterna som kan skicka data till en Digital Twins tjänst via den [Azure IoT-enhetens SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Stegvisa instruktioner om hur du med publicera enheter, finns det [självstudie om du vill distribuera och konfigurera digitala Twins](tutorial-facilities-setup.md). På ett ögonblick är stegen:

- Distribuera en Digital Twins-instans från den [Azure-portalen](https://portal.azure.com).
- Skapa blanksteg i grafen.
- Skapa en IoT Hub-resurs och tilldela den till ett blanksteg i diagrammet.
- Skapa enheter och sensorer i grafen och tilldela dem till utrymmen som skapades i föregående steg.
- Skapa en matcher för att filtrera telemetrimeddelanden baserat på villkor.
- Skapa en [användardefinierad funktion](concepts-user-defined-functions.md), och tilldela den till ett blanksteg i diagrammet för anpassad bearbetning av dina telemetrimeddelanden.
- Tilldela en roll så att den användardefinierade funktionen att komma åt graph-data.
- Hämta anslutningssträngen för IoT Hub-enhet från digitala Twins Management API: erna.
- Konfigurera enhetens anslutningssträng på enheten med Azure IoT-enhetens SDK.

Lär dig hur du hämtar anslutningssträngen för IoT Hub-enhet från digitala Twins Management API i avsnitten nedan. Du också lära dig hur du använder IoT Hub-telemetri meddelandeformat för att skicka telemetri om sensor-baserade. Digitala Twins kräver varje typ av telemetri som den tar emot som ska associeras med en sensor inom spatial diagrammet. Det här kravet ser till att data bearbetas och dirigeras inom rätt spatial kontext.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Hämta anslutningssträngen för IoT Hub-enhet från API Management

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Gör en GET-anrop till API-enhet med en `includes=ConnectionString` parametern för att hämta anslutningssträngen för IoT Hub-enhet. Filtrera efter enheten GUID eller maskinvaru-ID för att hitta den angivna enheten.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_GUID* | Enhets-ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parametervärde | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Enhetens maskinvaru-ID |

Kopiera enhetens i svarets nyttolast **connectionString** egenskapen. Du kan använda den för när du anropar Azure IoT-enhetens SDK att skicka data till Digital Twins.

## <a name="device-to-cloud-message"></a>Enhet-till-moln-meddelande

Du kan anpassa din enhets meddelandeformat och för att anpassa lösningens behov. Använd valfri datakontrakt kan serialiseras i en bytematris eller en dataström som stöds av den [Azure IoT Device klienten Message klass, meddelande (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Meddelandet kan vara anpassade binärformat föredrar, så länge du avkoda datakontrakt i en motsvarande en användardefinierad funktion. Det är endast ett krav för en enhet till moln-meddelande. Du måste ha en uppsättning egenskaper för att kontrollera att meddelandet dirigeras korrekt till motor för händelsebearbetning.

### <a name="telemetry-properties"></a>Egenskaper för telemetri

 Nyttolasten innehållet i en **meddelande** kan vara valfri data upp till 256 KB stora. Det finns några krav som förväntat för egenskaperna för den [ `Message.Properties` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typen. Tabellen visar de obligatoriska och valfria egenskaper som stöds av systemet.

| Egenskapsnamn | Värde | Krävs | Beskrivning |
|---|---|---|---|
| **DigitalTwins-telemetri** | 1.0 | Ja | Ett konstantvärde som identifierar ett meddelande i systemet. |
| **DigitalTwins SensorHardwareId** | `string(72)` | Ja | En unik identifierare för sensorn som skickar den **meddelande**. Det här värdet måste matcha ett objekts **HardwareId** -egenskapen för systemet att behandla den. Till exempel `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nej | En [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formaterad datumsträng som identifierar tiden samplar för nyttolasten. Till exempel `2018-09-20T07:35:00.8587882-07:00`. |
| **Korrelations-ID** | `string` | Nej | En UUID som har använt för att spåra händelser i systemet. Till exempel `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Skicka meddelandet till Digital Twins

Använd DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) eller [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) anrop för att skicka meddelandet till Digital Twins.

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Digital Twins databearbetning och användardefinierade funktioner, läsa [Azure Digital Twins bearbetning och användardefinierade funktioner](concepts-user-defined-functions.md).
