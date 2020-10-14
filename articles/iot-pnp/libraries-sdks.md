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
ms.openlocfilehash: 6ea9440c153e26e36aa17b55c4cb712dd08d4508
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042685"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK: er för IoT Plug and Play

IoT Plug and Play-bibliotek och SDK: er gör det möjligt för utvecklare att skapa IoT-lösningar med olika programmeringsspråk på flera plattformar. Följande tabell innehåller länkar till exempel och snabb starter som hjälper dig att komma igång:

## <a name="device-sdks"></a>Enhets-SDK: er

| Språk | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| C-enhet | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-c.md) | [Referens](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-enhet | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Ansluta till IoT Hub](quickstart-connect-device-csharp.md) | [Referens](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java-enhet | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ansluta till IoT Hub](quickstart-connect-device-java.md) | [Referens](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python-enhet | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-python.md) | [Referens](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Nod-enhet | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-node.md) | [Referens](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Inbäddad C-enhet | Saknas | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Exempel](howto-use-embedded-c.md#samples) | [Använda Embedded C](howto-use-embedded-c.md) | Saknas

## <a name="service-sdks"></a>Tjänst-SDK:er

| Plattform  | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| .NET – IoT Hub tjänst | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Saknas | [Referens](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Tjänsten Java-IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Saknas | [Referens](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Node-IoT Hub tjänst | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Saknas | [Referens](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python – Digitals dubbla tjänster | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-python.md) | Saknas |
| Nod-Digitals dubbla tjänster | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-node.md) | Saknas |

## <a name="next-steps"></a>Nästa steg

För att testa SDK: er och bibliotek, se  [Guide för utvecklare](concepts-developer-guide-device-csharp.md) och [snabb](quickstart-connect-device-c.md) Starter för enheter och [snabb starter för tjänsten](quickstart-service-node.md).