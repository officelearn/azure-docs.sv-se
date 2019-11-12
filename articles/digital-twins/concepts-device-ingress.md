---
title: Enhets anslutning och telemetri ingångar – Azure Digitals dubblare | Microsoft Docs
description: Översikt över hur du förflyttar en enhet med Azure Digitals
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4e7a179980abb164754941f345d2ee9ae7f5c9de
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930392"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Enhetsanslutning och inkommande telemetri

Telemetri-data som skickas av enheter och sensorer utgör stamnätet i alla IoT-lösningar. Hur du representerar dessa olika resurser och hanterar dem inom ramen för en plats är Chief-problem i IoT app-utveckling. Azure Digitals dubbla steg fören klar processen att utveckla IoT-lösningar genom att enhets enheter och sensorer med ett geografiskt informations diagram.

Kom igång genom att skapa en Azure IoT Hub-resurs i roten för det spatiala diagrammet. Med IoT Hub-resursen kan du skicka meddelanden från alla enheter under rot utrymmet. När IoT Hub har skapats registrerar du enheter med sensorer i den digitala dubbla instansen. Enheterna kan skicka data till en digital enhets tjänst via [Azure IoT-enhetens SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

En steg-för-steg-guide om hur du hanterar enheter finns i [självstudien för att distribuera och konfigurera digitala dubbla](tutorial-facilities-setup.md). Du kan snabbt och enkelt följa stegen:

- Distribuera en Digitals dubbla instanser från [Azure Portal](https://portal.azure.com).
- Skapa blank steg i grafen.
- Skapa en IoT Hub resurs och tilldela den till ett utrymme i grafen.
- Skapa enheter och sensorer i grafen och tilldela dem till de utrymmen som skapats i föregående steg.
- Skapa en matchning för att filtrera telemetri-meddelanden baserat på villkor.
- Skapa en [användardefinierad funktion](concepts-user-defined-functions.md)och tilldela den till ett blank steg i grafen för anpassad bearbetning av telemetri-meddelanden.
- Tilldela en roll för att tillåta den användardefinierade funktionen att komma åt graf-data.
- Hämta anslutnings strängen för IoT Hub enhet från de digitala API: erna för hantering av digitala dubbla.
- Konfigurera enhets anslutnings strängen på enheten med Azure IoT-enhetens SDK.

I följande avsnitt får du lära dig hur du hämtar den IoT Hub enhets anslutnings strängen från Digitals hanterings-API. Du lär dig också hur du använder meddelande formatet IoT Hub telemetri för att skicka sensorbaserade telemetri. Digitala enheter kräver varje telemetri som den tar emot för att associeras med en sensor i det spatiala diagrammet. Detta krav säkerställer att data bearbetas och dirigeras inom lämplig rums kontext.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Hämta anslutnings strängen för IoT Hub enhet från hanterings-API: et

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Hämta enhets-API: et med en `includes=ConnectionString` parameter för att hämta anslutnings strängen för IoT Hub enheten. Filtrera efter enhetens GUID eller maskinvaru-ID för att hitta den aktuella enheten.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_GUID* | Enhets-ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parametervärde | Ersätt med |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Enhetens maskinvaru-ID |

Kopiera enhetens **ConnectionString** -egenskap i svarets nytto Last. Du använder den när du anropar Azure IoT-enhetens SDK för att skicka data till digitala dubbla.

## <a name="device-to-cloud-message"></a>Meddelande från enhet till moln

Du kan anpassa enhetens meddelande format och nytto Last så att den passar din lösnings behov. Använd alla data kontrakt som kan serialiseras i en byte mat ris eller data ström som stöds av [klient meddelande klassen i Azure IoT-enheten, meddelande (byte [] ByteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Meddelandet kan vara ett anpassat binärt format som du väljer så länge du avkodar data kontraktet i en motsvarande användardefinierad funktion. Det finns bara ett krav för ett meddelande från enhet till moln. Behåll en uppsättning egenskaper för att se till att ditt meddelande dirigeras korrekt till bearbetnings motorn.

### <a name="telemetry-properties"></a>Egenskaper för telemetri

 Nytto Last innehållet i ett **meddelande** kan vara godtyckligt data upp till 256 kB. Det finns några krav som kan förväntas för [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typens egenskaper. Tabellen visar de obligatoriska och valfria egenskaper som stöds av systemet.

| Egenskapsnamn | Värde | Krävs | Beskrivning |
|---|---|---|---|
| **DigitalTwins – telemetri** | 1.0 | Ja | Ett konstant värde som identifierar ett meddelande i systemet. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ja | En unik identifierare för sensorn som skickar **meddelandet**. Värdet måste matcha ett objekts **HardwareId** -egenskap för att systemet ska kunna bearbeta det. Till exempel `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nej | En [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) -formaterad datum sträng som identifierar nytto lastens samplings tid. Till exempel `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Nej | Ett UUID som används för att spåra händelser i systemet. Till exempel `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Skicka meddelandet till digitala dubbla

Använd DeviceClient- [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) eller [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) -anropet för att skicka meddelandet till digitala dubbla.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure Digitals data behandling och användardefinierade funktioner kan du läsa mer i [Azure Digitals data behandling och användardefinierade funktioner](concepts-user-defined-functions.md).
