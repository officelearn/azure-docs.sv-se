---
title: Ansluta en hallon Pi till Azure IoT Suite med verkliga sensorer C | Microsoft Docs
description: Använd Microsoft Azure IoT-startpaket för Raspberry Pi 3 och Azure IoT Suite. Använd C om du vill ansluta din hallon Pi till fjärranslutna övervakningslösning skicka telemetri från sensorer till molnet och svara på metoderna som anropas från instrumentpanelen lösning.
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
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012010"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>Anslut din hallon Pi 3 till fjärranslutna övervakningslösning och skicka telemetri från en verklig sensor med hjälp av C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Den här kursen visar hur du använder Microsoft Azure IoT-startpaket för hallon Pi 3 för att utveckla en läsare för temperatur- och fuktighetskonsekvens som kan kommunicera med molnet. I självstudiekursen används:

- Raspbian OS programmeringsspråket C och Microsoft Azure IoT-SDK för C att implementera en exempel-enhet.
- IoT Suite fjärråtkomst övervakning förkonfigurerade lösning som molnbaserade serverdelen.

## <a name="overview"></a>Översikt

I den här kursen kan du utföra följande steg:

- Distribuera en instans av fjärråtkomst övervakning förkonfigurerade lösningen till din Azure-prenumeration. Det här steget kan du automatiskt distribuerar och konfigurerar Azure-tjänster.
- Konfigurera din enhet och sensorer för kommunikation med datorn och den fjärranslutna övervakningslösning.
- Uppdatera enheten exempelkod för att ansluta till den fjärranslutna övervakningslösning och skicka telemetri som kan visas på instrumentpanelen för lösningen.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Fjärråtkomst övervakningslösning etablerar en mängd olika Azure-tjänster i din Azure-prenumeration. Distributionen visar en verklig enterprise-arkitektur. Ta bort din instans av förkonfigurerade lösningen vid azureiotsuite.com för att undvika onödiga Azure-förbrukningen avgifter, när du är klar med den. Om du behöver den förkonfigurerade lösningen, kan du enkelt återskapa den. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Hämta och konfigurera exemplet

Du kan nu hämta och konfigurera fjärråtkomst övervakning klientprogrammet på din hallon Pi.

### <a name="clone-the-repositories"></a>Klona databaser

Om du inte redan har gjort klona krävs databaser genom att köra följande kommandon i en terminal på din Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>Uppdatera anslutningssträngen enhet

Öppna källfilen exempel i den **nano** redigeraren med följande kommando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

Leta upp följande rader:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Ersätta platshållarvärdena med enheten och IoT-hubb information du skapade och sparade i början av den här kursen. Spara dina ändringar (**Ctrl-O**, **RETUR**) och avsluta redigeraren (**Ctrl + X**).

## <a name="build-the-sample"></a>Bygga exemplet

Installera de nödvändiga paketen för Microsoft Azure IoT-enhet SDK för C genom att köra följande kommandon i en terminal på hallon Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Du kan nu skapa exempellösningen uppdaterade på hallon Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Du kan nu köra det här exempelprogrammet på hallon Pi. Ange kommandot:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Följande exempel på utdata är ett exempel på utdata som du ser i Kommandotolken på hallon Pi:

![Utdata från Raspberry Pi-app][img-raspberry-output]

Tryck på **Ctrl-C** avsluta programmet när som helst.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Nästa steg

Besök den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) fler exempel och dokumentation om Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
