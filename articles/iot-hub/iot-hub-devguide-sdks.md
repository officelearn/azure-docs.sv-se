---
title: Förstå Azure IoT-SDK | Microsoft Docs
description: 'Utvecklarhandbok - information om och länkar till olika Azure IoT-enheten och tjänsten SDK: erna som du kan använda för att skapa enhetsappar och backend-appar.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2380e72083ec0c9453db991be5fbcc89310cee17
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda SDK: er för Azure IoT-hubb

Det finns två typer av software development Kit (SDK) för att arbeta med IoT-hubb:

* **Enheten SDK** gör att du kan skapa appar som körs på din IoT-enheter. De här apparna skicka telemetri för din IoT-hubb och du kan också ta emot meddelanden, jobb, metoden eller dubbla uppdateringar från din IoT-hubb.

* **SDK-tjänsten** kan du hantera din IoT-hubb och (valfritt) skicka meddelanden, schemalägga, anropa direkt metoder eller skicka önskade egenskapen uppdateringar till IoT-enheter.

Lär dig mer om fördelarna med att utveckla med Azure IoT SDK [här][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT-enhet SDK

Microsoft Azure IoT-enhet SDK innehåller kod som underlättar skapande av enheter och program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub-enhet SDK för .NET: 
* Installera från [Nuget][lnk-nuget-csharp-device]
* [Källkoden][lnk-dotnet-sdk]
* [API-referens][lnk-dotnet-ref]

Azure IoT Hub-enhet SDK för C: som skrivits i ANSI C (C99) för överföring och bred plattformskompatibilitet
* Installera från [lgh get, MBED, Arduino IDE eller Nuget][lnk-c-package]
* [Källkoden][lnk-c-sdk]
* [API-referens][lnk-c-ref]

Azure IoT Hub-enhet SDK för Java: 
* Lägg till [Maven] [ lnk-maven-device] projekt
* [Källkoden][lnk-java-sdk]
* [API-referens][lnk-java-ref]

Azure IoT Hub-enhet SDK för Node.js: 
* Installera från [npm][lnk-npm-device]
* [Källkoden][lnk-node-sdk]
* [API-referens][lnk-node-ref]

Azure IoT Hub-enhet SDK för Python: 
* Installera från [pip][lnk-pip-device]
* [Källkoden][lnk-python-sdk]

> [!NOTE]
> Se Viktigt-filerna i GitHub-lagringsplatser för information om språk och plattformsspecifika paketet chefer för att installera binärfiler och beroenden på utvecklingsdatorn.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS-plattformen och maskinvarukompatibilitet

Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i [Azure certifierad för IoT-enhet katalogen] [ lnk-certified] eller enskilda databasen.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK

Azure IoT service SDK innehåller kod för att underlätta skapa program som kommunicerar direkt med IoT-hubb att hantera enheter och säkerhet.

Azure IoT-hubb service SDK för .NET:
* Ladda ned från [Nuget][lnk-nuget-csharp-service]
* [Källkoden][lnk-dotnet-sdk]
* [API-referens][lnk-dotnet-service-ref]

Azure IoT-hubb service SDK för Java: 
* Lägg till [Maven] [ lnk-maven-service] projekt
* [Källkoden][lnk-java-sdk]
* [API-referens][lnk-java-service-ref]

Azure IoT-hubb service SDK för Node.js: 
* Ladda ned från [npm][lnk-npm-service]
* [Källkoden][lnk-node-sdk]
* [API-referens][lnk-node-service-ref]

Azure IoT-hubb service SDK för Python: 
* Ladda ned från [pip][lnk-pip-service]
* [Källkoden][lnk-python-sdk]

Azure IoT-hubb service SDK för C: 
* Ladda ned från [lgh get, MBED, Arduino IDE eller Nuget][lnk-c-package]
* [Källkoden][lnk-c-sdk]

> [!NOTE]
> Se Viktigt-filerna i GitHub-lagringsplatser för information om språk och plattformsspecifika paketet chefer för att installera binärfiler och beroenden på utvecklingsdatorn.


## <a name="next-steps"></a>Nästa steg

Andra referensavsnitten i den här IoT-hubb Utvecklarhandbok inkluderar:

* [IoT-hubbslutpunkter][lnk-devguide-endpoints]
* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning][lnk-devguide-query]
* [Kvoter och begränsning][lnk-devguide-quotas]
* [IoT-hubb MQTT stöd][lnk-devguide-mqtt]
* [IoT Hub REST API-referens][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
