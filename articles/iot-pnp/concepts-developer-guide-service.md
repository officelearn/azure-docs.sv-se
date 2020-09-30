---
title: Guide till service Developer – IoT Plug and Play | Microsoft Docs
description: Beskrivning av IoT-Plug and Play för tjänst utvecklare
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7728af831b26bff19f347e5b85db6420e7966ed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580728"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Plug and Play service Developer Guide

Med IoT-Plug and Play kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

Med IoT-Plug and Play kan du använda enheter som har meddelat sitt modell-ID med IoT-hubben. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

Om du vill använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb använder du antingen en av IoT-tjänstens SDK: er eller IoT Hub REST API:

## <a name="service-sdks"></a>Tjänst-SDK:er

Använd Azure IoT service SDK: er i din lösning för att interagera med enheter och moduler. Du kan till exempel använda tjänst-SDK: erna för att läsa och uppdatera dubbla egenskaper och anropa kommandon. Språk som stöds är C#, Java, Node.js och python.

Med tjänst-SDK: er kan du komma åt enhets information från en lösning, till exempel ett skriv bord eller ett webb program. Tjänst-SDK: erna innehåller två namn rymder och objekt modeller som du kan använda för att hämta modell-ID:

- IoT Hub-tjänstens klient.
- Klient för Digitals sammanflätade tjänster.

| Språk | IoT Hub tjänst klient | Klient för Digitals dubbla tjänster |
| -------- | ---------------------- | ---------------------------- |
| C#       | [Dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples/Thermostat/Program.cs)| [Urvalsundersökningar](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample/thermostat-service-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/service/Thermostat.java)| [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Dokumentation](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Dokumentation](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) |
| Python   | [Dokumentation](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Dokumentation](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) | 

## <a name="rest-api"></a>REST-API

I följande exempel används IoT Hub REST API för att interagera med en ansluten IoT Plug and Play-enhet. Den aktuella versionen av API: t är `2020-09-30` . Lägg `?api-version=2020-05-31` till i dina rest PI-anrop.

> [!NOTE]
> Modulernas dubbla stöds inte för närvarande av `digitalTwins` API: et.

Om din termostat-enhet anropas `t-123` får du alla egenskaper för alla gränssnitt som implementerats av enheten med ett REST API GET-anrop:

```REST
GET /digitalTwins/t-123
```

Det här anropet inkluderar JSON `$metadata.$model` -egenskapen med modell-ID: t som visas i enheten.

Alla egenskaper för alla gränssnitt nås med `GET /DigitalTwin/{device-id}` REST API-mallen där `{device-id}` är identifieraren för enheten:

```REST
GET /digitalTwins/{device-id}
```

Du kan anropa kommandon för IoT Plug and Play Device direkt. Om `Thermostat` komponenten i `t-123` enheten har ett `restart` kommando kan du anropa den med ett REST API post-anrop:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Oftare kan kommandon anropas med hjälp av den här REST API mallen:

- `device-id`: enhetens identifierare.
- `component-name`: namnet på gränssnittet från avsnittet Implements i enhetens kapacitets modell.
- `command-name`: namnet på kommandot.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Digitalt flätat definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell komponenter](./concepts-components.md)
- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)
