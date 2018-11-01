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
ms.openlocfilehash: 9daca1876a291cb1f726e7c0eb4840c57f5c54c8
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741325"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda Azure IoT Hub SDK: er

Det finns tre kategorier av software development Kit (SDK) för att arbeta med IoT Hub:

* **SDK: er för enhetens** hjälper dig att skapa appar som körs på dina IoT-enheter med hjälp av enhetsklienten eller modulen klienten. De här apparna skicka telemetri till din IoT-hubb och du kan också ta emot meddelanden, jobb, metod eller uppdateringar för enhetstvilling från IoT hub.  Du kan också använda modulen klienten för att skapa [moduler](../iot-edge/iot-edge-modules.md) för [Azure IoT Edge-körningen](../iot-edge/about-iot-edge.md).

* **Tjänst-SDK: er** gör det möjligt att hantera din IoT-hubb och du kan också skicka meddelanden, schemalägga jobb, anropa direktmetoder eller skickar uppdateringar för önskad egenskap till din IoT-enheter eller moduler.

* **Device Provisioning SDKs** gör det möjligt att etablera enheter till IoT-hubben med hjälp av den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).

Lär dig mer om den [fördelarna med att utveckla med Azure IoT SDK: er](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure SDK: er för IoT-enheter

Microsoft Azure IoT-enheter i SDK: er innehåller kod som underlättar för att skapa enheter och program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub device SDK för .NET: 

* Installera från [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modulreferens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub device SDK för C, skriven i ANSI C (C99) för portabilitet och bred plattformskompatibilitet:

* Installera från [apt-get, MBED, Arduino IDE eller Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)
* [API-referens](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Modulreferens](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Azure IoT Hub device SDK för Java: 

* Lägg till i [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Modulreferens](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

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

> [!NOTE]
> Finns i readme-filerna i GitHub-lagringsplatser för information om att använda språk och plattformsspecifika pakethanterare för att installera binärfilerna och beroenden på utvecklingsdatorn.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS-plattformar och maskinvarukompatibilitet

Plattformar som stöds för SDK: erna finns i [Plattformsstödet för Azure IoT SDK: er](iot-hub-device-sdk-platform-support.md).

Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i den [Azure Certified för IoT-enhetskatalog](https://catalog.azureiotsuite.com/) eller enskilda lagringsplats.

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK: er

Azure IoT service SDK: erna innehåller kod för att underlätta att bygga program som interagerar direkt med IoT Hub att hantera enheter och säkerhet.

Azure IoT Hub service SDK för .NET:

* Ladda ned från [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub service SDK för Java: 

* Lägg till i [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) projekt
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

## <a name="device-provisioning-sdks"></a>SDK: er för enhetsetablering

Den **Microsoft Azure etablering SDK: er** gör det möjligt att etablera enheter till IoT-hubben med hjälp av den [Device Provisioning-tjänsten](../iot-dps/about-iot-dps.md).

Azure etablerar enheten och tjänsten SDK: er för C#:

* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Azure etablerar enheten och tjänsten SDK: er för Java:

* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Azure etablerar enheten och tjänsten SDK: er för Node.js:

* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Azure etablerar enheten och tjänsten SDK: er för Python:

* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Azure Provisioning-tjänst och enhet SDK för C:

* [Etablering enhetsklienten SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Provisioning service client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Nästa steg

Azure IoT SDK: er tillhandahåller också en uppsättning verktyg för att underlätta utveckling:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): ett plattformsoberoende kommandoradsverktyg för att diagnostisera problem som rör anslutning med IoT Hub.
* [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): ett Windows-skrivbordsprogram kan ansluta till din IoT-hubb.

Andra referensavsnitten i det här utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
* [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-support](iot-hub-mqtt-support.md)
* [IoT Hub REST API-referens](/rest/api/iothub/)
* [Support för Azure IoT SDK-plattform](iot-hub-device-sdk-platform-support.md)
