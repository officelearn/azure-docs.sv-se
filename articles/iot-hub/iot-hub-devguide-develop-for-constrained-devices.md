---
title: Azure IoT Hub Utveckla för begränsade enheter med IoT Hub C SDK
description: Utvecklarguide – vägledning om hur du utvecklar med Azure IoT SDK:er för begränsade enheter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733194"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Utveckla för begränsade enheter med Azure IoT C SDK

Azure IoT Hub C SDK är skriven i ANSI C (C99), vilket gör det väl lämpat att driva en mängd olika plattformar med liten disk- och minnesfotavtryck. Det rekommenderade RAM-minnet är minst 64 KB, men det exakta minnesavtrycket beror på vilket protokoll som används, antalet anslutningar som öppnas samt plattformens mål.
> [!NOTE]
> * Azure IoT C SDK publicerar regelbundet information om resursförbrukning för att hjälpa till med utvecklingen.  Besök vårt [GitHub-arkiv](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) och granska det senaste riktmärket.
>

C SDK finns i paketformulär från apt-get, NuGet och MBED. Om du vill rikta begränsade enheter kanske du vill skapa SDK lokalt för din målplattform. Den här dokumentationen visar hur du tar bort vissa funktioner för att minska c SDK:s fotavtryck med [cmake](https://cmake.org/). Dessutom beskrivs i den här dokumentationen de bästa programmeringsmodellerna för att arbeta med begränsade enheter.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Bygga C SDK för begränsade enheter

Skapa C SDK för begränsade enheter.

### <a name="prerequisites"></a>Krav

Följ den här [installationsguiden för C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) för att förbereda din utvecklingsmiljö för att bygga C SDK. Innan du kommer till steget för att bygga med cmake, kan du åberopa cmake flaggor för att ta bort oanvända funktioner.

### <a name="remove-additional-protocol-libraries"></a>Ta bort tilläggsprotokollbibliotek

C SDK stöder fem protokoll idag: MQTT, MQTT över WebSocket, AMQPs, AMQP över WebSocket och HTTPS. De flesta scenarier kräver ett till två protokoll som körs på en klient, därför kan du ta bort protokollbiblioteket som du inte använder från SDK. Ytterligare information om hur du väljer lämpligt kommunikationsprotokoll för ditt scenario finns i [Välj ett IoT Hub-kommunikationsprotokoll](iot-hub-devguide-protocols.md). MQTT är till exempel ett lättviktsprotokoll som ofta är bättre lämpat för begränsade enheter.

Du kan ta bort AMQP- och HTTP-biblioteken med följande cmake-kommando:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Ta bort SDK-loggningsfunktion

C SDK ger omfattande loggning hela för att hjälpa till med felsökning. Du kan ta bort loggningsfunktionen för produktionsenheter med följande cmake-kommando:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Ta bort uppladdning till blob-funktion

Du kan ladda upp stora filer till Azure Storage med hjälp av den inbyggda funktionen i SDK. Azure IoT Hub fungerar som en avsändare till ett associerat Azure Storage-konto. Du kan använda den här funktionen för att skicka mediefiler, stora telemetribatchar och loggar. Du kan få mer information om [hur du laddar upp filer med IoT Hub](iot-hub-devguide-file-upload.md). Om programmet inte kräver den här funktionen kan du ta bort den här funktionen med följande cmake-kommando:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Löparremsa på Linux-miljö

Om dina binärfiler körs på Linux-system kan du utnyttja [stripkommandot](https://en.wikipedia.org/wiki/Strip_(Unix)) för att minska storleken på det slutliga programmet efter kompilering.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmeringsmodeller för begränsade enheter

Titta sedan på programmeringsmodeller för begränsade enheter.

### <a name="avoid-using-the-serializer"></a>Undvik att använda Serializer

C SDK har en valfri [C SDK-seriesändare](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), som gör att du kan använda deklarativa mappningstabeller för att definiera metoder och enhetstvillingegenskaper. Serialiseraren är utformad för att förenkla utvecklingen, men den lägger till omkostnader, vilket inte är optimalt för begränsade enheter. I det här fallet kan du använda primitiva klient-API:er och tolka JSON med hjälp av en lätt tolk som [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Använd det nedre lagret _(LL)_

C SDK stöder två programmeringsmodeller. En uppsättning har API:er med en LL-infix, som står för det nedre lagret. _LL_ Den här uppsättningen API:er är lägre vikt och snurrar inte upp arbetstrådar, vilket innebär att användaren manuellt måste styra schemaläggningen. För enhetsklienten finns till exempel _LL-API:erna_ i den här [huvudfilen](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

En annan uppsättning API:er utan _LL-index_ kallas bekvämlighetslagret, där en arbetstråd snurras automatiskt. Till exempel finns api:erna för bekvämlighetslager för enhetsklienten i den här [IoT-enhetsklienthuvudfilen](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). För begränsade enheter där varje extra tråd kan ta en betydande procentandel av systemresurserna bör du överväga att använda _LL_ API:er.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure IoT C SDK-arkitektur:
-    [Azure IoT C SDK-källkod](https://github.com/Azure/azure-iot-sdk-c/)
-    [Azure IoT-enhet SDK för C introduktion](iot-hub-device-sdk-c-intro.md)
