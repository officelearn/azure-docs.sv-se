---
title: 'Förstå Azure IoT SDK: er | Microsoft-dokument'
description: Utvecklarguide – information om och länkar till de olika Azure IoT-enhets- och tjänst-SDK:er som du kan använda för att skapa enhetsappar och backend-appar.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: be5fae45ee513dddf002995ce9c37c6b6565f50c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258396"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda Azure IoT Hub SDK:er

Det finns två kategorier av programvaruutvecklingspaket (SDK: er) för att arbeta med IoT Hub:

* **Med IoT Hub Device SDK:er** kan du skapa appar som körs på dina IoT-enheter med hjälp av enhetsklient eller modulklient. Dessa appar skickar telemetri till din IoT-hubb och kan ta emot meddelanden, jobb, metoder eller tvillinguppdateringar från din IoT-hubb.  Du kan också använda modulklient för att skapa [moduler](../iot-edge/iot-edge-modules.md) för [Azure IoT Edge-körning .](../iot-edge/about-iot-edge.md)

* **Med IoT Hub Service SDK:er** kan du skapa serverdelsprogram för att hantera din IoT-hubb och eventuellt skicka meddelanden, schemalägga jobb, anropa direkta metoder eller skicka önskade egenskapsuppdateringar till dina IoT-enheter eller moduler.

Dessutom tillhandahåller vi också en uppsättning SDK:er för att arbeta med [enhetsetableringstjänsten](../iot-dps/about-iot-dps.md).
* **Med etablering av enhets-SDK:er** kan du skapa appar som körs på dina IoT-enheter för att kommunicera med enhetsetableringstjänsten.

* **Med etableringstjänst-SDK:er** kan du skapa serverd-program för att hantera dina registreringar i enhetsetableringstjänsten.

Läs mer om [fördelarna med att utveckla med Azure IoT SDK:er](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilitet mellan OS-plattform och maskinvara

Plattformar som stöds för SDK:erna finns i [Azure IoT SDKs Platform Support](iot-hub-device-sdk-platform-support.md).

Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/) eller enskilda databasen.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub Device SDK:er

Microsoft Azure IoT-enheten SDK:er innehåller kod som underlättar att skapa program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub-enhet SDK för .NET: 

* Ladda ner från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Namnområdet är Microsoft.Azure.Devices.Clients, som innehåller IoT Hub Device Clients (DeviceClient, ModuleClient).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modulreferens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub-enhet SDK för C (ANSI C - C99):

* Installera från [apt-get, MBED, Arduino IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilera C-enhetSDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Modulreferens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portera C SDK till andra plattformar](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Utvecklardokumentation](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) för information om korskompilering, komma igång på olika plattformar, etc.
* [Information om azure IoT Hub C SDK-resursförbrukning](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub-enheten SDK för Java: 

* Lägg till [i Maven-projektet](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modulreferens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub-enhet SDK för nod.js: 

* Installera från [npm](https://www.npmjs.com/package/azure-iot-device)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modulreferens](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub-enheten SDK för Python: 

* Installera från [pip](https://pypi.org/project/azure-iot-device/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* [API-referens](https://docs.microsoft.com/python/api/azure-iot-device)

Azure IoT Hub-enhet SDK för iOS: 

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-referens: se [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub-tjänst SDK:er

Azure IoT-tjänsten SDK:er innehåller kod för att underlätta att skapa program som interagerar direkt med IoT Hub för att hantera enheter och säkerhet.

Azure IoT Hub-tjänst SDK för .NET:

* Ladda ner från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Namnområdet är Microsoft.Azure.Devices, som innehåller IoT Hub Service Clients (RegistryManager, ServiceClients).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub-tjänst SDK för Java: 

* Lägg till [i Maven-projektet](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub-tjänst SDK för nod.js: 

* Ladda ner från [npm](https://www.npmjs.com/package/azure-iothub)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub-tjänst SDK för Python: 

* Ladda ner från [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Azure IoT Hub-tjänst SDK för C: 

* Ladda ner från [apt-get, MBED, Arduino IDE eller NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub-tjänst SDK för iOS: 

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Se readme-filerna i GitHub-databaserna för information om hur du använder språk- och plattformsspecifika pakethanterare för att installera binärfiler och beroenden på din utvecklingsdator.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure-etablering av SDK:er

**Med Microsoft Azure-etablerings-SDK:erna** kan du etablera enheter till din IoT-hubb med hjälp av [enhetsetableringstjänsten](../iot-dps/about-iot-dps.md).

Azure-etableringsenhet och tjänst-SDK:er för C#:

* Ladda ner från [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) och [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) från NuGet.
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Azure-etableringsenhet och tjänst-SDK:er för C:

* Installera från [apt-get, MBED, Arduino IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure-etableringsenhet och tjänst-SDK:er för Java:

* Lägg till [i Maven-projektet](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Källkod](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Azure-etableringsenhet och tjänst-SDK:er för Node.js:

* [Källkod](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-referens](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Ladda ner [Device SDK](https://badge.fury.io/js/azure-iot-provisioning-device) och [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) från npm

Azure-etableringsenhet och tjänst-SDK:er för Python:

* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* Ladda ner [Device SDK](https://pypi.org/project/azure-iot-device/) och [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) från pip

## <a name="next-steps"></a>Nästa steg

Azure IoT SDK:er tillhandahåller också en uppsättning verktyg som hjälper dig att utveckla:
* [iothub-diagnostik](https://github.com/Azure/iothub-diagnostics): ett plattformsoberoende kommandoradsverktyg för att diagnostisera problem relaterade till anslutning med IoT Hub.
* [azure-iot-explorer:](https://github.com/Azure/azure-iot-explorer)ett plattformsoberoende skrivbordsprogram för att ansluta till din IoT-hubb och lägga till/hantera/kommunicera med IoT-enheter.

Relevanta dokument relaterade till utveckling med hjälp av Azure IoT SDK:er:
* Lär dig mer om [hur du hanterar anslutning och tillförlitliga meddelanden](iot-hub-reliability-features-in-sdks.md) med hjälp av IoT Hub SDK:er.
* Läs mer om hur du [utvecklar för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Support för Azure IoT SDK-plattformen](iot-hub-device-sdk-platform-support.md)


Andra referensavsnitt i den här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [Support för IoT Hub MQTT](iot-hub-mqtt-support.md)
* [IoT Hub REST API-referens](/rest/api/iothub/)
