---
title: Azure IoT-hubb utveckla för begränsad enheter | Microsoft Docs
description: 'Utvecklarhandbok - vägledning om hur du utvecklar med SDK: er för Azure IoT för begränsad enheter.'
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 62065b78e3f8191c6423ba9dba4a8f7d16fad114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655570"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Utveckla för begränsad enheter med hjälp av Azure IoT-SDK

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Utveckla med Azure IoT-hubb C SDK
Azure IoT-hubb C SDK är skriven i ANSI-C (C99), vilket gör det bra att använda en mängd olika plattformar med kompakta för disk- och minne.  Det rekommenderade RAM-MINNET är minst 64 KB, men det exakta minneskravet beror på det protokoll som används, antalet anslutningar som öppnas, samt den plattform som mål.

C-SDK är tillgängligt i paketet form från lgh get, NuGet och MBED.  Om du vill rikta begränsad enheter kanske du vill skapa SDK lokalt för målplattformen. Den här dokumentationen visar hur du tar bort vissa funktioner för att minska storleken på C-SDK med hjälp av [cmake][lnk-cmake].  Dessutom kan beskrivs den här dokumentationen bästa praxis och programmeringsmodeller för att arbeta med begränsad enheter.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Skapa C-SDK för begränsad enheter
#### <a name="prerequisites"></a>Förutsättningar
Följer detta [inställningsguiden] [ lnk-devbox-setup] att förbereda din utvecklingsmiljö för att skapa C-SDK.  Du kan anropa cmake flaggorna för att ta bort oanvända funktioner innan du kommer till steg för att bygga med cmake.

#### <a name="remove-additional-protocol-libraries"></a>Ta bort ytterligare protokoll-bibliotek
C SDK stöder fem protokoll idag: MQTT, MQTT via WebSocket, AMQPs, AMQP via WebSocket och HTTPS.    De flesta scenarier kräver en till två protokoll som körs på en klient, därför kan du ta bort protocol-biblioteket som du inte använder från SDK.  Mer information om hur du väljer rätt kommunikationsprotokollet för ditt scenario kan hittas i den här [dokument][lnk-choosing-protocol].  Till exempel är MQTT ett enkelt protokoll som passar bättre ofta begränsad enheter.

Du kan ta bort AMQP och HTTP-bibliotek med följande kommando i cmake:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Ta bort SDK loggningsfunktioner
C-SDK innehåller omfattande loggning i hela som hjälp med felsökning. Du kan ta bort loggningsfunktioner för produktion enheter med hjälp av kommandot cmake:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Ta bort överför för att blob-funktion
Du kan överföra stora filer till Azure Storage med hjälp av inbyggda funktioner i SDK.  Azure IoT-hubb som fungerar som en dispatcher till ett associerat Azure Storage-konto.  Du kan använda den här funktionen för att skicka mediefiler, stora telemetri batchar och loggar.  Lär dig mer om att överföra filer med IoT-hubben i det här [dokument][lnk-hub-file-upload].  Om ditt program inte kräver den här funktionen kan du ta bort den här funktionen med hjälp av kommandot cmake:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Körs remsans på Linux-miljö
Om din binärfiler köras på Linux-system, kan du använda den [remsans kommandot] [ lnk-strip] att minska storleken på sista programmet efter kompilering.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Programmering modeller för begränsad enheter
#### <a name="avoid-using-the-serializer"></a>Undvik att använda funktionen för serialisering
C-SDK innehåller en valfri [serialiseraren][lnk-serializer], där du kan använda deklarativa mappningstabeller för att definiera metoder och egenskaper för enhet dubbla.  Serialiseraren är utformat för att förenkla utvecklingen, men det lägger till omkostnader, som inte är optimala för begränsad enheter.  I det här fallet bör du använda primitiva klientens API: er och parsa json med hjälp av en förenklad parser som [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Använd det undre lagret (_lla_)
C-SDK stöder två programmeringsmodeller.  En uppsättning har API: er med en _lla_ infix, vilket står för lägre nivå.  Denna uppsättning API: er är tunt och få upp hastigheten inte arbetstrådar, vilket innebär att användaren måste du manuellt vill styra schemaläggning.  Till exempel för klienten för enheter i _lla_ API: er finns i det här [huvudfilen](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  En annan uppsättning API: er utan den _lla_ index kallas bekvämlighet lagret, där en arbetstråd roterade automatiskt.  Till exempel bekvämlighet lagret API: er för enhetsklienten kan hittas i den här [huvudfilen](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  För begränsad enheter där varje extra tråd kan ta en betydande del av systemresurser, Överväg att använda _lla_ API: er.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure IoT C SDK-arkitektur:
-   [Azure IoT C SDK-källkod](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure IoT-enhet SDK C introduktion](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson