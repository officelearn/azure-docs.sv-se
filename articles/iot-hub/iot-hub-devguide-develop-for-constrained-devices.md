---
title: Azure IoT Hub utveckla för begränsad enheter med hjälp av IoT Hub C SDK | Microsoft Docs
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
origin.date: 05/24/2018
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: 7788bca621a59ec8cdfe36edf73a99efca8c460c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61320963"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Utveckla för begränsad enheter med hjälp av Azure IoT C SDK

Azure IoT Hub C SDK är skriven i ANSI-C (C99), vilket gör det passar bra för att driva en rad olika plattformar med kompakta disk och minne. Det rekommendera ram-MINNET är minst 64 KB, men den exakta minnesavtrycket som krävs beror på det protokoll som används, antal öppnade anslutningar, samt den plattform som mål.
> [!NOTE]
> * Azure IoT C SDK och publicerar därför regelbundet förbrukning av resursinformation som underlättar utveckling.  Besök vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) och granska de senaste prestandamått.
>

C SDK är tillgänglig i form av paketet från apt-get, NuGet och MBED. Om du vill rikta begränsad enheter kanske du vill skapa SDK: N lokalt för din målplattform. Den här dokumentationen visar hur du tar bort vissa funktioner för att minska utrymmet för C SDK med hjälp av [cmake](https://cmake.org/). Dessutom kan beskrivs den här dokumentationen bästa praxis programmeringsmodeller för att arbeta med begränsad enheter.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Skapa C SDK för begränsad enheter

Bygga SDK för C för begränsad enheter.

### <a name="prerequisites"></a>Nödvändiga komponenter

Följ den här [C SDK installationsguide](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) till Förbered din utvecklingsmiljö för att skapa C SDK. Innan du kommer till steget för att bygga med cmake, kan du anropa cmake flaggorna för att ta bort oanvända funktioner.

### <a name="remove-additional-protocol-libraries"></a>Ta bort bibliotek för ytterligare protokoll

C SDK har stöd för fem protokoll idag: MQTT MQTT via WebSocket, AMQPs, AMQP via WebSocket och HTTPS. De flesta scenarier kräver en till två protokoll som körs på en klient, därför kan du ta bort protocol-biblioteket som du inte använder från SDK. Mer information om hur du väljer rätt kommunikationsprotokoll för ditt scenario finns i [väljer du en IoT Hub-protokollet](iot-hub-devguide-protocols.md). Till exempel är MQTT ett enkelt protokoll som passar bättre ofta begränsad enheter.

Du kan ta bort AMQP och HTTP-bibliotek med följande cmake-kommando:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Ta bort SDK loggningsfunktioner

C SDK innehåller omfattande loggning i hela för att hjälpa till med felsökning. Du kan ta bort loggningsfunktioner för produktionsenheter som använder följande cmake-kommandot:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Ta bort ladda upp till blob funktion

Du kan överföra stora filer till Azure Storage med hjälp av den inbyggda funktionen i SDK. Azure IoT Hub fungerar som en dispatcher till ett associerat Azure Storage-konto. Du kan använda den här funktionen för att skicka mediefiler, stora telemetri batchar och loggar. Du kan få mer information [Överför filer med IoT Hub](iot-hub-devguide-file-upload.md). Om programmet inte kräver den här funktionen kan du ta bort den här funktionen med följande cmake-kommandot:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Remsans som körs på Linux-miljö

Om din binärfiler kör på Linux-system, kan du utnyttja den [kommandot tar bort](https://en.wikipedia.org/wiki/Strip_(Unix)) att minska storleken på sista programmet efter kompilering.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmeringsmodeller för begränsad enheter

Nu ska titta på programmeringsmodeller för begränsad enheter.

### <a name="avoid-using-the-serializer"></a>Undvik att använda serialiseraren

C SDK har en valfri [C SDK serialiserare](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), där du kan använda deklarativa mappningstabeller för att definiera metoder och tvillingegenskaper. Serialiserare är utformad för att förenkla utvecklingen, men det ger omkostnader, som inte är optimala för begränsad enheter. I det här fallet bör du använda primitiva klientens API: er och parsa JSON genom att använda en förenklad parser som [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-ll"></a>Använd det undre lagret (_lla_)

C SDK stöder två programmeringsmodeller. En uppsättning har API: er med en _lla_ infix, vilket står för undre lager. Den här uppsättningen med API: er är ljusare vikt och inte skapa arbetstrådar, vilket innebär att användaren måste du manuellt vill styra schemaläggning. Till exempel för enhetsklienten, den _lla_ API: er finns i den här [huvudfil](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

En annan uppsättning API: erna utan den _lla_ index kallas bekvämlighet-lagret, där en arbetstråd kunde automatiskt. Till exempel bekvämlighet lager API: er för enhetsklienten kan hittas i den här [IoT Device Client huvudfil](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). För begränsad enheter där varje extra tråd kan ta en betydande del av systemresurser, Överväg att använda _lla_ API: er.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure IoT C SDK-arkitektur:
- [Källkoden för Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
- [Azure IoT-enhetens SDK för C-introduktion](iot-hub-device-sdk-c-intro.md)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: iot-hub-devguide-protocols.md
[lnk-hub-file-upload]: iot-hub-devguide-file-upload.md
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson