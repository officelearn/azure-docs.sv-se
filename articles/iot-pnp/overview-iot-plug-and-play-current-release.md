---
title: IoT Plug and Play aktuell utgåva | Microsoft Docs
description: Lär dig vad som ingår i den aktuella IoT Plug and Play-versionen.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583566"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Vad finns i den aktuella IoT Plug and Play-versionen

I den här artikeln sammanfattas de verktyg, SDK: er och API: er som har stöd för den aktuella IoT Plug and Play-versionen. Versions nummer som visas återspeglar versions numret vid tidpunkten för att IoT Plug and Play skulle vara allmänt tillgängligt. Versions nummer kan öka efter versionen.

## <a name="modeling-language"></a>Modellerings språk

[Digitals definitions språk (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Läs mer om hur IoT Plug and Play-enheter fungerar med DTDL i [IoT plug and Play-konventioner](concepts-convention.md).

## <a name="tools-and-utilities"></a>Verktyg och hjälpmedel

- 0.12.0 för Azure IoT Explorer.

    Mer information finns i [Installera och använda Azure IoT Explorer](howto-use-iot-explorer.md).

- VS Code-tillägg 1.0.0.

    Mer information finns i [Installera och använda redigerings verktygen för DTDL](howto-use-dtdl-authoring-tools.md).

- 1.0.0 för Visual Studio 2019-tillägg.

    Mer information finns i [Installera och använda redigerings verktygen för DTDL](howto-use-dtdl-authoring-tools.md).

- 0.10.0 för Azure CLI IoT-tillägg.

    Mer information finns i [Installera och använda Azure IoT-tillägget för Azure CLI](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > Azure IoT-tillägget innehåller kommandon som hjälper dig att certifiera enheter. Se `az iot product -h`.



## <a name="libraries-and-sdks"></a>Bibliotek och SDK:er

Mer information om bibliotek och SDK: er finns i [Microsoft SDK: er för IoT plug and Play](libraries-sdks.md).

- C-enhets-SDK [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- .NET-enhets-SDK [NuGet Microsoft. Azure. devices. client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Java-enhets [-SDK maven IoT-Device-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python-enhet SDK [pip Azure-IoT-Device v-2.2.0](https://pypi.org/project/azure-iot-device/)
- Node.js Device SDK [NPM Azure-IoT-Device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET-IoT Hub tjänst [NuGet Microsoft. Azure. Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java-IoT Hub service [maven IoT-service-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js-IoT Hub tjänst [NPM Azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/Digitals service [pip – Azure-IoT-Hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- DTDL Model parser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

### <a name="preview"></a>Förhandsgranskning

- Azure SDK för Embedded [1.0.0 – för hands version. 5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- ÅTERSTÄLLNINGS tider IoT mellanprogramvara [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>REST API:er

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Läs mer i [IoT plug and Play Developer Guide](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

IoT Plug and Play stöds av IoT Hub i alla regioner. IoT Plug and Play stöds endast av IoT-hubbar av standard eller kostnads fri nivå.

## <a name="announcements"></a>Annonsering

För aktuella och tidigare IoT Plug and Play-meddelanden, se följande blogg inlägg:

- [Offentlig för hands versions uppdatering (publicerades den 29 augusti 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Förbered och certifiera dina enheter för IoT Plug and Play (publiceras den 26 augusti 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Plug and Play finns nu i för hands version (publicerades den 22 augusti 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Utveckla med Azure IoT Central och IoT Plug and Play (publicerades den 7 maj 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

