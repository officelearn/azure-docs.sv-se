---
title: Ansluta en Raspberry Pi till Azure IoT Suite med hjälp av C med simulerad telemetri | Microsoft Docs
description: Använd Microsoft Azure IoT-startpaket för Raspberry Pi 3 och Azure IoT Suite. Använd C för att ansluta Raspberry Pi till fjärrövervakningslösningen, skickar simulerad telemetri till molnet och svara på metoderna som anropas från lösningens instrumentpanel.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 19b16bff7874a03578b8766668c431553da0d875
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635616"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-c"></a>Anslut Raspberry Pi 3 till lösningen för fjärrövervakning och skickar simulerad telemetri med hjälp av C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Den här självstudien visar hur du använder Raspberry Pi 3 för att simulera temperatur- och data som skickas till molnet. I självstudiekursen används:

- Raspbian OS programmeringsspråket C och Microsoft Azure IoT SDK för C för att implementera en exempel-enhet.
- I IoT Suite för den förkonfigurerade lösningen som molnbaserad serverdel.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Lösningen för fjärrövervakning etablerar en uppsättning Azure-tjänster i Azure-prenumerationen. Distributionen återspeglar en verklig företagsarkitektur. Om du vill undvika onödiga Azure-förbrukning kostnader genom att ta bort din instans av den förkonfigurerade lösningen på azureiotsuite.com när du är klar med den. Om du behöver den förkonfigurerade lösningen kan återskapa du enkelt den. Läs mer om att minskas när fjärrövervakningslösningen körs [konfigurera Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Hämta och konfigurera exemplet

Du kan nu hämta och konfigurera fjärråtkomst övervakning klientprogrammet på Raspberry Pi.

### <a name="clone-the-repositories"></a>Klona lagringsplatser

Om du inte redan gjort det, klona de nödvändiga databaserna genom att köra följande kommandon i en terminal på din Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Uppdatera enhetens anslutningssträng

Öppna källfilen exemplet i den **nano** redigeraren med följande kommando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/remote_monitoring/remote_monitoring.c
```

Leta upp följande rader:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Ersätt platshållarvärdena med enheten och IoT Hub information du skapade och sparade i början av den här självstudien. Spara dina ändringar (**Ctrl-O**, **RETUR**) och avsluta redigeraren (**Ctrl + X**).

## <a name="build-the-sample"></a>Skapa exemplet

Installera de nödvändiga paketen för Microsoft Azure IoT Device SDK för C genom att köra följande kommandon i en terminal på Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Du kan nu skapa den uppdaterade exempellösningen på Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
```

Du kan nu köra exempelprogrammet på Raspberry Pi. Ange kommandot:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Följande exempel på utdata är ett exempel på utdata som du ser i Kommandotolken på Raspberry Pi:

![Utdata från Raspberry Pi-app][img-raspberry-output]

Tryck på **Ctrl-C** att avsluta programmet när som helst.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Nästa steg

Gå till den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) för fler exempel och dokumentation om Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
