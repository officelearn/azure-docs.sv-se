---
title: 'Förstå Azure IoT SDK: er | Microsoft Docs'
description: 'Utvecklarguide – information om och länkar till olika Azure-IoT-enheten och tjänsten SDK: erna som du kan använda för att bygga appar för enheter och backend-appar.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: f5d493628df68e0afbe05137b0d7386d6c40f7d4
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605750"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda Azure IoT Hub SDK: er

Det finns tre kategorier av software development Kit (SDK) för att arbeta med IoT Hub:

* **SDK: er för enhetens** hjälper dig att skapa appar som körs på dina IoT-enheter med hjälp av enhetsklienten eller modulen klienten. De här apparna skicka telemetri till din IoT-hubb och du kan också ta emot meddelanden, jobb, metod eller uppdateringar för enhetstvilling från IoT hub.  Du kan också använda modulen klienten för att skapa [moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) för [Azure IoT Edge-körningen](https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge).

* **Tjänst-SDK: er** gör det möjligt att hantera din IoT-hubb och du kan också skicka meddelanden, schemalägga jobb, anropa direktmetoder eller skickar uppdateringar för önskad egenskap till din IoT-enheter eller moduler.

* **Device Provisioning SDKs** gör det möjligt att etablera enheter till IoT-hubben med hjälp av den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).

Lär dig mer om fördelarna med att utveckla med Azure IoT SDK: er [här][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure SDK: er för IoT-enheter

Microsoft Azure IoT-enheter i SDK: er innehåller kod som underlättar för att skapa enheter och program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub device SDK för .NET: 
* Installera från [Nuget][lnk-nuget-csharp-device]
* [Källkod][lnk-dotnet-sdk]
* [API-referens][lnk-dotnet-ref]
* [Modulreferens] [https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet]

Azure IoT Hub device SDK för C: som skrivits i ANSI C (C99) för portabilitet och bred plattformskompatibilitet
* Installera från [apt-get, MBED, Arduino IDE eller Nuget][lnk-c-package]
* [Källkod][lnk-c-sdk]
* [API-referens][lnk-c-ref]
* [Modulreferens] [https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h]

Azure IoT Hub device SDK för Java: 
* Lägg till i [Maven] [ lnk-maven-device] projekt
* [Källkod][lnk-java-sdk]
* [API-referens][lnk-java-ref]
* [Modulreferens] [https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable]

Azure IoT Hub device SDK för Node.js: 
* Installera från [npm][lnk-npm-device]
* [Källkod][lnk-node-sdk]
* [API-referens][lnk-node-ref]
* [Modulreferens] [https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest]

Azure IoT Hub device SDK för Python: 
* Installera från [pip][lnk-pip-device]
* [Källkod][lnk-python-sdk]

Azure IoT Hub device SDK för iOS: 
* Installera från [CocoaPod][lnk-cocoa-device]
* [Exempel][lnk-ios-sample]

> [!NOTE]
> Finns i readme-filerna i GitHub-lagringsplatser för information om att använda språk och plattformsspecifika pakethanterare för att installera binärfilerna och beroenden på utvecklingsdatorn.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS-plattformar och maskinvarukompatibilitet

Plattformar som stöds för SDK: erna finns i den här [dokumentet](iot-hub-device-sdk-platform-support.md).
Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i den [Azure Certified för IoT-enhetskatalog] [ lnk-certified] eller enskilda lagringsplats.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK: er

Azure IoT service SDK: erna innehåller kod för att underlätta att bygga program som interagerar direkt med IoT Hub att hantera enheter och säkerhet.

Azure IoT Hub service SDK för .NET:
* Ladda ned från [Nuget][lnk-nuget-csharp-service]
* [Källkod][lnk-dotnet-sdk]
* [API-referens][lnk-dotnet-service-ref]

Azure IoT Hub service SDK för Java: 
* Lägg till i [Maven] [ lnk-maven-service] projekt
* [Källkod][lnk-java-sdk]
* [API-referens][lnk-java-service-ref]

Azure IoT Hub service SDK för Node.js: 
* Ladda ned från [npm][lnk-npm-service]
* [Källkod][lnk-node-sdk]
* [API-referens][lnk-node-service-ref]

Azure IoT Hub service SDK för Python: 
* Ladda ned från [pip][lnk-pip-service]
* [Källkod][lnk-python-sdk]

Azure IoT Hub service SDK för C: 
* Ladda ned från [apt-get, MBED, Arduino IDE eller Nuget][lnk-c-package]
* [Källkod][lnk-c-sdk]

Azure IoT Hub service SDK för iOS: 
* Installera från [CocoaPod][lnk-cocoa-service]
* [Exempel][lnk-ios-sample]

> [!NOTE]
> Finns i readme-filerna i GitHub-lagringsplatser för information om att använda språk och plattformsspecifika pakethanterare för att installera binärfilerna och beroenden på utvecklingsdatorn.

## <a name="device-provisioning-sdks"></a>SDK: er för enhetsetablering

Den **Microsoft Azure etablering SDK: er** gör det möjligt att etablera enheter till IoT-hubben med hjälp av den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).

Azure etablerar enheten och tjänsten SDK: er för C#:
* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Azure etablerar enheten och tjänsten SDK: er för Java:
* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning-device-client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Azure etablerar enheten och tjänsten SDK: er för Node.js:
* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Azure etablerar enheten och tjänsten SDK: er för Python:
* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Azure Provisioning-tjänst och enhet SDK för C:
* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning/service)

## <a name="next-steps"></a>Nästa steg

Azure IoT SDK: er tillhandahåller också en uppsättning verktyg för att underlätta utveckling:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): ett plattformsoberoende kommandoradsverktyg för att diagnostisera problem som rör anslutning med IoT Hub.
* [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): ett Windows-skrivbordsprogram kan ansluta till din IoT-hubb.

Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter][lnk-devguide-endpoints]
* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning][lnk-devguide-query]
* [Kvoter och begränsningar][lnk-devguide-quotas]
* [IoT Hub MQTT-support][lnk-devguide-mqtt]
* [IoT Hub REST API-referens][lnk-rest-ref]
* [Plattformsstödet för Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

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
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient
