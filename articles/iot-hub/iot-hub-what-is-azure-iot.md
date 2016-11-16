---
title: "Azure-lösningar för Sakernas Internet (IoT, Internet of Things) | Microsoft Docs"
description: "En översikt över IoT i Azure, inklusive en exempellösningsarkitektur och hur den relaterar till Azure IoT Hub, enhets-SDK:er och förkonfigurerade lösningar"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2dbf639abfa505eb329769bcc346efb5f1db443e


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Nästa steg
Azure IoT Hub är en Azure-tjänst som möjliggör säker och tillförlitlig dubbelriktad kommunikation mellan serverdelen för ditt program och flera miljoner enheter. Det låter programmets serverdel:

* Ta emot telemetri i stor skala från dina enheter.
* Vidarebefordra data från dina enheter till en dataström-händelseprocessor.
* Ta emot filöverföringar från enheter.
* Skicka kommandon från moln till enhet för specifika enheter.

Du kan använda IoT Hub för att implementera din egen backend-lösning. IoT Hub tillhandahåller även ett enhetsidentitetsregister som används för att etablera enheter och deras säkerhetsreferenser, samt deras behörigheter för anslutning till hubben. Läs mer om IoT-hubb i [Vad är IoT-hubb?][lnk-iot-hub].

Information om hur Azure IoT-hubb tillhandahåller standardbaserad enhetshantering som hjälper dig att hantera, konfigurera och uppdatera dina enheter finns i [Översikt över enhetshantering med IoT Hub][lnk-device-management].

Om du vill implementera klientprogram på många olika enhetsspecifika maskinvaruplattformar och operativsystem kan du använda enhets-SDK:er för IoT. Enhets-SDK:erna för IoT innehåller bibliotek som gör det lätt att skicka telemetri till en IoT-hubb och att ta emot kommandon från molnet till enheten. När du använder SDK:erna kan du välja mellan olika nätverksprotokoll för att kommunicera med IoT Hub. Mer information finns i [Information om enhets-SDK:er][lnk-device-sdks].

Information om hur du börjar skriva kod och köra några exempel finns i självstudien [Komma igång med IoT-hubb][lnk-getstarted].

Du kanske också är intresserad av [Azure IoT Suite][lnk-iot-suite], som är en samling förkonfigurerade lösningar. Med IoT Suite kan du snabbt komma igång och skala IoT-projekt för att hantera vanliga IoT-scenarier – till exempel fjärrövervakning, tillgångshantering och förebyggande underhåll.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO2-->


