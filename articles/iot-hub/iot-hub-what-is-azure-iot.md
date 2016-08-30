<properties
 pageTitle="Azure-lösningar för Sakernas Internet (IoT, Internet of Things) | Microsoft Azure"
 description="En översikt över IoT i Azure, inklusive en exempellösningsarkitektur och hur den relaterar till Azure IoT Hub, enhets-SDK:er och förkonfigurerade lösningar"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Nästa steg

Azure IoT Hub är en Azure-tjänst som möjliggör säker och tillförlitlig dubbelriktad kommunikation mellan serverdelen för ditt program och flera miljoner enheter. Tjänsten gör att serverdelen för ditt program kan ta emot telemetri i hög skala från dina enheter, dirigera dessa data till en processor för dataströmshändelser samt även skicka ”molnet till enhet”-kommandon till specifika enheter. Du kan använda IoT Hub för att implementera din egen backend-lösning. IoT Hub tillhandahåller även ett enhetsidentitetsregister som används för att etablera enheter och deras säkerhetsreferenser, samt deras behörigheter för anslutning till hubben. Du kan läsa mer här:

- [Vad är IoT Hub?][lnk-iot-hub]
- [Komma igång med IoT Hub][lnk-getstarted]
- [Översikt över enhetshantering i Azure IoT Hub][lnk-device-management]

Om du vill implementera klientprogram på många olika enhetsspecifika maskinvaruplattformar och operativsystem kan du använda enhets-SDK:er för IoT. Enhets-SDK:erna för IoT innehåller bibliotek som gör det lätt att skicka telemetri till en IoT-hubb och att ta emot kommandon från molnet till enheten. När du använder SDK:erna kan du välja mellan ett antal olika nätverksprotokoll för att kommunicera med IoT Hub. Mer information finns i [Information om enhets-SDK:er][lnk-device-sdks].

Du kanske också är intresserad av [Azure IoT Suite][lnk-iot-suite], som är en samling förkonfigurerade lösningar. Med IoT Suite kan du snabbt komma igång och skala IoT-projekt för att hantera vanliga IoT-scenarier – till exempel fjärrövervakning, tillgångshantering och förebyggande underhåll.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md


<!--HONumber=jun16_HO2-->


