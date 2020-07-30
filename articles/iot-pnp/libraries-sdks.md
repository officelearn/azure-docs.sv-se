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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407803"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK: er för IoT Plug and Play

IoT Plug and Play-bibliotek och SDK: er gör det möjligt för utvecklare att skapa IoT-lösningar med olika programmeringsspråk på flera plattformar. Följande tabell innehåller länkar till exempel och snabb starter som hjälper dig att komma igång:

## <a name="device-sdks-ga"></a>Enhets-SDK: er (GA)

| Språk | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| C-enhet | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-c.md) | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-enhet | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Ansluta till IoT Hub](quickstart-connect-device-csharp.md) | [Referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-enhet | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ansluta till IoT Hub](quickstart-connect-device-java.md) | [Referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-enhet | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-python.md) | [Referens](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Nod-enhet | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device-node.md) | [Referens](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Enhets-SDK (för hands version)

| Språk | Kod lagring/exempel |
|---|---|
|Azure SDK för Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure återställnings tider IoT-mellanprogram| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Guider för att komma igång med Azure återställnings tider | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Tjänst-SDK: er (för hands version)

| Språk | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| För hands version av .NET – IoT Hub tjänsten | [NuGet 1.27.1 – Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Exempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | E.t. | E.t. |
| För hands version av Java-IoT Hub tjänsten | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | E.t. | E.t. |
| För hands version av Node-IoT Hub tjänsten | [NPM 1.12.4-PnP-Refresh. 4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | E.t. | E.t. |
| För hands version av python – IoT Hub/Digital Multiservice | [pip 2.2.1 rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-python.md) | E.t. |
| Förhands granskning av nodens digitala dubbla tjänster | [NPM 1.0.0-PnP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service-node.md) | E.t. |

## <a name="next-steps"></a>Nästa steg

För att testa SDK: er och bibliotek, se [Guide för utvecklare](concepts-developer-guide.md) och [snabb](quickstart-connect-device-c.md) Starter för enheter och [snabb starter för tjänsten](quickstart-service-node.md).
