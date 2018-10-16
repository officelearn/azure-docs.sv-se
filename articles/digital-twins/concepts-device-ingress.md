---
title: Enhetens anslutning och telemetriingång med Azure Digital Twins | Microsoft Docs
description: Översikt över onboarding en enhet med Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324196"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Enhetens anslutning och telemetriingång

Dessa data skickas från enheterna och sensorerna utgör basen för alla IoT-lösningar. Därför är som representerar dessa olika resurser och hantera dem inom ramen för en plats chief nämnvärt IoT-apputveckling. Azure Digital Twins förenklar utveckla IoT-lösningar genom att förena enheter och sensorer med ett spatial intelligence-diagram.

Du kommer igång en `IoTHub` resurs ska skapas i roten av spatial diagram, vilket gör att alla enheter under rot-utrymme för att skicka meddelanden. När IoT-hubben har skapats och enheter med sensorer som har registrerats inom Digital Twins instansen kan enheterna kan börja skicka data till en Digital Twins tjänst via den [Azure IoT Device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

En stegvis guide för onboarding-enheter finns i den [självstudie om du vill distribuera och konfigurera digitala Twins](tutorial-facilities-setup.md). På ett ögonblick är stegen:

- Distribuera en Azure Digital Twins-instans från den [Azure-portalen](https://portal.azure.com)
- Skapa blanksteg i grafen
- Skapa en `IoTHub` resursen och tilldela den till ett blanksteg i grafen
- Skapa enheter och sensorer i diagrammet och tilldela dem till utrymmen som skapades i föregående steg
- Skapa en matcher för att filtrera telemetrimeddelanden baserat på villkor
- Skapa en [ **användardefinierad funktion** ](concepts-user-defined-functions.md) och tilldela den till ett blanksteg i diagrammet för anpassad bearbetning av dina telemetrimeddelanden
- Tilldela en roll så att den användardefinierade funktionen att komma åt graph-data
- Hämta anslutningssträngen för IoT Hub-enhet från digitala Twins Management API: er
- Konfigurera enhetens anslutningssträng på enheten med Azure IoT SDK

Nedan får du lära dig hur du hämtar anslutningssträngen för IoT Hub-enhet från digitala Twins Management API och hur du använder IoT Hub-telemetri meddelandeformat för att skicka telemetri om sensor-baserade. Digitala Twins kräver varje typ av telemetri för att vara kopplad till en sensor inom spatial diagrammet för att säkerställa att data bearbetas och dirigeras inom rätt spatial kontext.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Hämta anslutningssträngen för IoT Hub-enhet från API Management

Gör en GET-anrop till API-enhet med en `includes=ConnectionString` parametern för att hämta anslutningssträngen för IoT Hub-enhet. Du kan filtrera efter enhets GUID eller maskinvaru-ID att hitta en given enhet:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management |
| `yourDeviceGuid` | Enhets-ID |
| `yourDeviceHardwareId` | Enhetens maskinvaru-ID |

Kopiera enhetens i svarets nyttolast `connectionString` egenskapen, som du använder när du anropar Azure IoT Device SDK för att skicka data till Azure Digital Twins.

## <a name="device-to-cloud-message"></a>Enheten molnmeddelandet

Du kan anpassa din enhets meddelandeformat och för att anpassa lösningens behov. Du kan använda valfri datakontrakt kan serialiseras i en bytematris eller en dataström som stöds av den [Azure IoT Device klienten Message klass, meddelande (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Meddelandet kan vara anpassade binärformat föredrar, så länge du avkoda datakontrakt i en motsvarande användardefinierade funktion. Det enda kravet för ett meddelande om enheten till molnet är att hantera en uppsättning egenskaper (se nedan) för att säkerställa att meddelandet dirigeras korrekt till motor för händelsebearbetning.

### <a name="telemetry-properties"></a>Egenskaper för telemetri

Medan nyttolast innehållet i en `Message` kan vara valfri data upp till 256 kb i storlek, det finns några krav på förväntat [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). De steg som beskrivs nedan återspeglar de obligatoriska och valfria egenskaper som stöds av systemet.

| Egenskapsnamn | Värde | Krävs | Beskrivning |
|---|---|---|---|
| DigitalTwins-telemetri | 1.0 | ja | Ett konstant värde som identifierar ett meddelande i systemet |
| DigitalTwins SensorHardwareId | `string(72)` | ja | En unik identifierare för sensor skickar den `Message`. Det här värdet måste matcha ett objekts `HardwareId` -egenskapen för systemet att behandla den. Till exempel, `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | nej | En [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formaterad datumsträng identifiera tiden samplar för nyttolasten. Till exempel, `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | nej | En `uuid` som kan användas för att spåra händelser i systemet. Till exempel, `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Skicka meddelandet till Digital Twins

Använd DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) eller [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) anrop för att skicka meddelandet till din digitala Twins-tjänst.

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins' databearbetning och användardefinierade funktioner, [Azure Digital Twins bearbetning och användardefinierade funktioner](concepts-user-defined-functions.md).

