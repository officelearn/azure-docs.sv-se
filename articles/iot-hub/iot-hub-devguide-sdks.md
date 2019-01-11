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
ms.openlocfilehash: 3bdb09de04ec5041d671f5af709a1a1e7133d141
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199518"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda Azure IoT Hub SDK: er

Det finns två typer av software development Kit (SDK) för att arbeta med IoT Hub:

* **SDK: er för IoT Hub-enheter** hjälper dig att skapa appar som körs på dina IoT-enheter med hjälp av enhetsklienten eller modulen klienten. De här apparna skicka telemetri till din IoT-hubb och du kan också ta emot meddelanden, jobb, metod eller uppdateringar för enhetstvilling från IoT hub.  Du kan också använda modulen klienten för att skapa [moduler](../iot-edge/iot-edge-modules.md) för [Azure IoT Edge-körningen](../iot-edge/about-iot-edge.md).

* **IoT Hub Service SDK** aktivera du kan skapa serverprogram att hantera din IoT-hubb och du kan också skicka meddelanden, schemalägga jobb, anropa direktmetoder eller skickar uppdateringar för önskad egenskap till din IoT-enheter eller moduler.

Dessutom erbjuder vi även en uppsättning SDK: er för att arbeta med den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).
* **Etablera enheten SDK: er** hjälper dig att skapa appar som körs på dina IoT-enheter att kommunicera med Device Provisioning-tjänsten.

* **Provisioning Service-SDK: er** hjälper dig att skapa serverprogram att hantera dina registreringar i Device Provisioning-tjänsten.

Lär dig mer om den [fördelarna med att utveckla med Azure IoT SDK: er](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>OS-plattformar och maskinvarukompatibilitet

Plattformar som stöds för SDK: erna finns i [Plattformsstödet för Azure IoT SDK: er](iot-hub-device-sdk-platform-support.md).

Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i den [Azure Certified för IoT-enhetskatalog](https://catalog.azureiotsuite.com/) eller enskilda lagringsplats.

## <a name="azure-iot-hub-device-sdks"></a>SDK: er för Azure IoT Hub-enheter

Microsoft Azure IoT-enhetens SDK: er innehåller kod som underlättar skapa program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub device SDK för .NET: 

* Ladda ned från [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Namnområdet är Microsoft.Azure.Devices.Clients som innehåller IoT Hub-Enhetsklienter (DeviceClient, ModuleClient).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modulreferens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub device SDK för C (ANSI-C - C99):

* Installera från [apt-get, MBED, Arduino IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilera C-enhets-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Modulreferens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Porta C SDK och andra plattformar](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Utvecklardokumentation](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) information om cross-kompilering, komma igång på olika plattformar, osv.

Azure IoT Hub device SDK för Java: 

* Lägg till i [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modulreferens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub device SDK för Node.js: 

* Installera från [npm](https://www.npmjs.com/package/azure-iot-device)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modulreferens](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub device SDK för Python: 

* Installera från [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* API-referens: se [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure IoT Hub device SDK för iOS: 

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-referens: se [C API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub Service SDK: er

Azure IoT service SDK: erna innehåller kod för att underlätta att bygga program som interagerar direkt med IoT Hub att hantera enheter och säkerhet.

Azure IoT Hub service SDK för .NET:

* Ladda ned från [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Namnområdet är Microsoft.Azure.Devices som innehåller IoT Hub-tjänsten på klienter (RegistryManager, ServiceClients).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub service SDK för Java: 

* Lägg till i [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub service SDK för Node.js: 

* Ladda ned från [npm](https://www.npmjs.com/package/azure-iothub)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub service SDK för Python: 

* Ladda ned från [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python)

Azure IoT Hub service SDK för C: 

* Ladda ned från [apt-get, MBED, Arduino IDE eller Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub service SDK för iOS: 

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Finns i readme-filerna i GitHub-lagringsplatser för information om att använda språk och plattformsspecifika pakethanterare för att installera binärfilerna och beroenden på utvecklingsdatorn.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure etablering SDK: er

Den **Microsoft Azure etablering SDK: er** gör det möjligt att etablera enheter till IoT-hubben med hjälp av den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).

Azure etablerar enheten och tjänsten SDK: er för C#:

* Ladda ned från [enhets-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) och [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) från NuGet.
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Azure Provisioning-tjänst och enhet SDK för C:

* Installera från [apt-get, MBED, Arduino IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure etablerar enheten och tjänsten SDK: er för Java:

* Lägg till i [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Azure etablerar enheten och tjänsten SDK: er för Node.js:

* [Källkod](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-referens](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Ladda ned [enhets-SDK](https://badge.fury.io/js/azure-iot-provisioning-device) och [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) från npm

Azure etablerar enheten och tjänsten SDK: er för Python:

* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* Ladda ned [enhets-SDK](https://pypi.org/project/azure-iot-provisioning-device-client/) och [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) från pip

## <a name="next-steps"></a>Nästa steg

Azure IoT SDK: er tillhandahåller också en uppsättning verktyg för att underlätta utveckling:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): ett plattformsoberoende kommandoradsverktyg för att diagnostisera problem som rör anslutning med IoT Hub.
* [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): ett Windows-skrivbordsprogram kan ansluta till din IoT-hubb.

Relevanta docs relaterade vid utveckling med Azure IoT SDK:
* Lär dig mer om [hur du hanterar anslutningar och tillförlitlig meddelandehantering](iot-hub-reliability-features-in-sdks.md) med IoT Hub SDK.
* Läs om hur du [utveckla för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Support för Azure IoT SDK-plattform](iot-hub-device-sdk-platform-support.md)


Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-support](iot-hub-mqtt-support.md)
* [IoT Hub REST API-referens](/rest/api/iothub/)
