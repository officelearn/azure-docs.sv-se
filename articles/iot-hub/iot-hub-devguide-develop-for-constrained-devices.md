---
title: Azure IoT Hub utveckla för begränsad enheter | Microsoft Docs
description: Utvecklarguide – vägledning för hur du utvecklar med Azure IoT SDK för begränsad enheter.
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
ms.openlocfilehash: 15fc5794c71428b5fb1036060af3e9c4a6890f4f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969070"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Utveckla för begränsad enheter med hjälp av Azure IoT SDK: er

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Utveckla med hjälp av Azure IoT Hub C SDK
Azure IoT Hub C SDK är skriven i ANSI-C (C99), vilket gör det passar bra för att driva en rad olika plattformar med kompakta disk och minne.  Det rekommendera ram-MINNET är minst 64 KB, men den exakta minnesavtrycket som krävs beror på det protokoll som används, antal öppnade anslutningar, samt den plattform som mål.

C SDK är tillgänglig i form av paketet från apt-get, NuGet och MBED.  Om du vill rikta begränsad enheter kanske du vill skapa SDK: N lokalt för din målplattform. Den här dokumentationen visar hur du tar bort vissa funktioner för att minska utrymmet för C SDK med hjälp av [cmake][lnk-cmake].  Dessutom kan beskrivs den här dokumentationen bästa praxis programmeringsmodeller för att arbeta med begränsad enheter.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Skapa C SDK för begränsad enheter
#### <a name="prerequisites"></a>Förutsättningar
Följ den här [installationsguide] [ lnk-devbox-setup] till Förbered din utvecklingsmiljö för att skapa C SDK.  Innan du kommer till steget för att bygga med cmake, kan du anropa cmake flaggorna för att ta bort oanvända funktioner.

#### <a name="remove-additional-protocol-libraries"></a>Ta bort bibliotek för ytterligare protokoll
C SDK har stöd för fem protokoll idag: MQTT, MQTT via WebSocket, AMQPs, AMQP via WebSocket och HTTPS.    De flesta scenarier kräver en till två protokoll som körs på en klient, därför kan du ta bort protocol-biblioteket som du inte använder från SDK.  Mer information om hur du väljer rätt kommunikationsprotokoll för ditt scenario finns i den här [dokumentet][lnk-choosing-protocol].  Till exempel är MQTT ett enkelt protokoll som passar bättre ofta begränsad enheter.

Du kan ta bort AMQP och HTTP-bibliotek med följande cmake-kommando:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Ta bort SDK loggningsfunktioner
C SDK innehåller omfattande loggning i hela för att hjälpa till med felsökning. Du kan ta bort loggningsfunktioner för produktionsenheter som använder följande cmake-kommandot:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Ta bort ladda upp till blob funktion
Du kan överföra stora filer till Azure Storage med hjälp av den inbyggda funktionen i SDK.  Azure IoT Hub fungerar som en dispatcher till ett associerat Azure Storage-konto.  Du kan använda den här funktionen för att skicka mediefiler, stora telemetri batchar och loggar.  Lär dig mer om hur du överför filer med IoT Hub i den här [dokumentet][lnk-hub-file-upload].  Om programmet inte kräver den här funktionen kan du ta bort den här funktionen med följande cmake-kommandot:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Remsans som körs på Linux-miljö
Om din binärfiler kör på Linux-system, kan du utnyttja den [kommandot tar bort] [ lnk-strip] att minska storleken på sista programmet efter kompilering.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Programmeringsmodeller för begränsad enheter
#### <a name="avoid-using-the-serializer"></a>Undvik att använda serialiseraren
C SDK har en valfri [serialiserare][lnk-serializer], där du kan använda deklarativa mappningstabeller för att definiera metoder och tvillingegenskaper.  Serialiserare är utformad för att förenkla utvecklingen, men det ger omkostnader, som inte är optimala för begränsad enheter.  I det här fallet bör du använda primitiva klientens API: er och parsa json genom att använda en förenklad parser som [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Använd det undre lagret (_lla_)
C SDK stöder två programmeringsmodeller.  En uppsättning har API: er med en _lla_ infix, vilket står för undre lager.  Den här uppsättningen med API: er är tunt och skapa inte arbetstrådar, vilket innebär att användaren måste du manuellt vill styra schemaläggning.  Till exempel för enhetsklienten, den _lla_ API: er finns i den här [huvudfil](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  En annan uppsättning API: erna utan den _lla_ index kallas bekvämlighet-lagret, där en arbetstråd kunde automatiskt.  Till exempel bekvämlighet lager API: er för enhetsklienten kan hittas i den här [huvudfil](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  För begränsad enheter där varje extra tråd kan ta en betydande del av systemresurser, Överväg att använda _lla_ API: er.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure IoT C SDK-arkitektur:
-   [Källkoden för Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure IoT-enhetens SDK för C-introduktion](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson