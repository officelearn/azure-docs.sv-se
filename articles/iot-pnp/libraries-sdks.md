---
title: 'IoT Plug and Play-bibliotek och SDK: er'
description: Information om de enhets-och tjänst bibliotek som är tillgängliga för att utveckla IoT Plug and Play-aktiverade lösningar.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1b534c6ccb3bfca6c7dddaec82d8794deae72ff2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651317"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK: er för IoT Plug and Play

IoT Plug and Play-bibliotek och SDK: er gör det möjligt för utvecklare att skapa IoT-lösningar med olika programmeringsspråk på flera plattformar. Följande tabell innehåller länkar till exempel och snabb starter som hjälper dig att komma igång:

## <a name="device-sdks"></a>Enhets-SDK: er

| Språk | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| C-enhet | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-c.md) | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-enhet | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Ansluta till IoT Hub](quickstart-connect-device-csharp.md) | [Referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java-enhet | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ansluta till IoT Hub](quickstart-connect-device-java.md) | [Referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python-enhet | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-python.md) | [Referens](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Nod-enhet | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-node.md) | [Referens](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Inbäddad C-enhet | E.t. | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Exempel](howto-use-embedded-c.md#samples) | [Använda Embedded C](howto-use-embedded-c.md) | E.t.

## <a name="service-sdks"></a>Tjänst-SDK:er

| Plattform  | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| .NET – IoT Hub tjänst | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | E.t. | [Referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Tjänsten Java-IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | E.t. | [Referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Node-IoT Hub tjänst | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | E.t. | [Referens](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python – Digitals dubbla tjänster | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-python.md) | E.t. |
| Nod-Digitals dubbla tjänster | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-node.md) | E.t. |

## <a name="next-steps"></a>Nästa steg

För att testa SDK: er och bibliotek, se  [Guide för utvecklare](concepts-developer-guide-device-csharp.md) och [snabb](quickstart-connect-device-c.md) Starter för enheter och [snabb starter för tjänsten](quickstart-service-node.md).
