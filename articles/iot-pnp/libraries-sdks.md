---
title: IoT Plug and Play-bibliotek och SDK:er
description: Information om enhets- och tjänstbiblioteken som är tillgängliga för att utveckla IoT Plug and Play-aktiverade lösningar.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064337"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT Plug and Play-bibliotek och SDK:er

IoT Plug and Play-biblioteken och SDK:erna gör det möjligt för utvecklare att skapa IoT-lösningar med hjälp av en mängd olika programmeringsspråk på flera plattformar. Följande tabell innehåller länkar till exempel och snabbstarter som hjälper dig att komma igång:

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft-bibliotek och SDK:er som stöds

| Plattform | Bibliotek/paket | Källkod | Exempel | Snabbstart | Referens |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Enhet SDK på apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitala Twin-klientprover](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Ansluta till IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Enhet SDK på Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitala Twin-klientprover](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Ansluta till IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Enhet SDK på EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitala Twin-klientprover](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Enhet SDK i Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitala Twin-klientprover](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Enhet SDK på CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitala Twin-klientprover](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitala twin-prover](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Ansluta till IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Referens](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Digitala twin-prover](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Ansluta till IoT Hub](./quickstart-connect-pnp-device-java.md) | [Referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM (NPM)](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitala twin-prover](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Ansluta till IoT Hub](./quickstart-connect-pnp-device-node.md) | [Referens](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Support för IoT Hub

IoT Plug and Play-enhetsfunktioner stöds endast av [kostnadsfria IoT-hubbar på nivånivå](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Nästa steg

Förutom enhetens SDK:er och bibliotek kan du använda REST-API:er för att interagera med modelldatabaserna.