---
title: "Förstå Azure IoT-SDK | Microsoft Docs"
description: "Utvecklarhandbok - information om och länkar till olika Azure IoT-enheten och tjänsten SDK: erna som du kan använda för att skapa enhetsappar och backend-appar."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e299de0953cefac925b0015a15983d25d456576f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-azure-iot-sdks"></a>Förstå och använda Azure IoT-SDK

Det finns tre kategorier av software development Kit (SDK) för att arbeta med IoT-hubb:

* **Enheten SDK** gör att du kan skapa appar som körs på din IoT-enheter. De här apparna skicka telemetri för din IoT-hubb och du kan också ta emot meddelanden från din IoT-hubb.

* **SDK-tjänsten** kan du hantera din IoT-hubb och (valfritt) skicka meddelanden till din IoT-enheter.

* **Azure IoT-Edge** låter dig skapa gateways för enheter som inte använder något av protokoll som stöds. Gateways kan också bearbeta meddelanden på gränsen.

SDK: er som stöder flera programmeringsspråk.

## <a name="azure-iot-device-sdks"></a>Azure IoT-enhet SDK

Microsoft Azure IoT-enhet SDK innehåller kod som underlättar skapande av enheter och program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Följande Azure IoT-enhet SDK: er kan hämtas från GitHub:

* [Azure IoT-enhet SDK för .NET][lnk-dotnet-device-sdk]
* [Azure IoT-enhet SDK för Java][lnk-java-device-sdk]
* [Azure IoT-enhet SDK för Node.js][lnk-node-device-sdk]
* [Azure IoT-enhet SDK för Python][lnk-python-device-sdk]
* [Azure IoT-enhet SDK för C] [ lnk-c-device-sdk] skrivs i ANSI C (C99) för överföring och bred plattformskompatibilitet. Det finns två enhet-klientbibliotek för C, på låg nivå **iothub_client** och **serialiseraren**.

> [!NOTE]
> Se Viktigt-filerna i GitHub-lagringsplatser för information om språk och plattformsspecifika paketet chefer för att installera binärfiler och beroenden på utvecklingsdatorn.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS-plattformen och maskinvarukompatibilitet

Mer information om SDK-kompatibilitet med specifika maskinvaruenheter finns i [Azure certifierad för IoT-enhet katalogen][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDK

Azure IoT service SDK innehåller kod för att underlätta skapa program som kommunicerar direkt med IoT-hubb att hantera enheter och säkerhet.

Följande Azure IoT-tjänst SDK: er kan hämtas från GitHub:

* [Azure IoT-tjänsten SDK för .NET][lnk-dotnet-service-sdk]
* [Azure IoT-tjänsten SDK för Java][lnk-java-service-sdk]
* [Azure IoT-tjänsten SDK för Node.js][lnk-node-service-sdk]
* [Azure IoT-tjänsten SDK för Python][lnk-python-service-sdk]
* [Azure IoT-tjänsten SDK för C][lnk-c-service-sdk]

> [!NOTE]
> Se Viktigt-filerna i GitHub-lagringsplatser för information om språk och plattformsspecifika paketet chefer för att installera binärfiler och beroenden på utvecklingsdatorn.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT-Edge innehåller infrastruktur och moduler för att skapa lösningar för IoT-gateway. Du kan utöka IoT kant om du vill skapa gateways som är skräddarsydda för slutpunkt till slutpunkt scenarier.

Du kan hämta [Azure IoT kant] [ lnk-iot-edge] från GitHub.

## <a name="online-api-reference-documentation"></a>Onlinedokumentation för API-referens

Följande lista innehåller länkar till online API-referensdokumentationen för Azure IoT-enhet, tjänst och gateway-bibliotek:

* [Sakernas (IoT) .NET Internet][lnk-dotnet-ref]
* [Azure IoT-enhet SDK för Java][lnk-java-ref]
* [Azure IoT-tjänsten SDK för Java][lnk-java-service-ref]
* [Azure IoT-enhet SDK för Node.js][lnk-node-ref]
* [Azure IoT-tjänsten SDK för Node.js][lnk-node-service-ref]
* [Azure IoT-enhet SDK för C][lnk-c-ref]
* [IoT-hubb REST][lnk-rest-ref]
* [Azure IoT kant][lnk-gateway-ref]

## <a name="next-steps"></a>Nästa steg

Andra referensavsnitten i den här IoT-hubb Utvecklarhandbok inkluderar:

* [IoT-hubbslutpunkter][lnk-devguide-endpoints]
* [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning][lnk-devguide-query]
* [Kvoter och begränsning][lnk-devguide-quotas]
* [IoT-hubb MQTT stöd][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
