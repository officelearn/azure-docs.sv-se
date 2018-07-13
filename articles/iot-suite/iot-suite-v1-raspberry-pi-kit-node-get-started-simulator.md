---
title: Ansluta en Raspberry Pi till Azure IoT Suite med hjälp av Node.js med simulerad telemetri | Microsoft Docs
description: Använd Microsoft Azure IoT-startpaket för Raspberry Pi 3 och Azure IoT Suite. Använda Node.js för att ansluta Raspberry Pi till fjärrövervakningslösningen, skickar simulerad telemetri till molnet och svara på metoderna som anropas från lösningens instrumentpanel.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 53297049fd36eae3839c6a8146afc336b8f5cd02
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676495"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>Anslut Raspberry Pi 3 till lösningen för fjärrövervakning och skickar simulerad telemetri med hjälp av Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Den här självstudien visar hur du använder Raspberry Pi 3 för att simulera temperatur- och data som skickas till molnet. I självstudiekursen används:

- Raspbian OS programmeringsspråket som Node.js och Microsoft Azure IoT SDK för Node.js att implementera en exempel-enhet.
- I IoT Suite för den förkonfigurerade lösningen som molnbaserad serverdel.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Lösningen för fjärrövervakning etablerar en uppsättning Azure-tjänster i Azure-prenumerationen. Distributionen återspeglar en verklig företagsarkitektur. Om du vill undvika onödiga Azure-förbrukning kostnader genom att ta bort din instans av den förkonfigurerade lösningen på azureiotsuite.com när du är klar med den. Om du behöver den förkonfigurerade lösningen kan återskapa du enkelt den. Läs mer om att minskas när fjärrövervakningslösningen körs [konfigurera Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Hämta och konfigurera exemplet

Du kan nu hämta och konfigurera fjärråtkomst övervakning klientprogrammet på Raspberry Pi.

### <a name="install-nodejs"></a>Installera Node.js

Om du inte redan gjort det, kan du installera Node.js på Raspberry Pi. Med IoT SDK för Node.js kräver version 0.11.5 av Node.js eller senare. Följande steg visar hur du installerar v6.10.2 Node.js på Raspberry Pi:

1. Använd följande kommando för att uppdatera Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommando för att ladda ned Node.js-binärfiler till Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Använd följande kommando för att installera binärfilerna:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Använd följande kommando för att verifiera att du har installerat Node.js v6.10.2 har:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Klona lagringsplatser

Om du inte redan gjort det, klona de nödvändiga databaserna genom att köra följande kommandon i en terminal på din Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Uppdatera enhetens anslutningssträng

Öppna källfilen exemplet i den **nano** redigeraren med följande kommando:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Leta reda på raden:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Ersätt platshållarvärdena med enheten och IoT Hub information du skapade och sparade i början av den här självstudien. Spara dina ändringar (**Ctrl-O**, **RETUR**) och avsluta redigeraren (**Ctrl + X**).

## <a name="run-the-sample"></a>Kör exemplet

Kör följande kommandon för att installera de nödvändiga paketen för exemplet:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

Du kan nu köra exempelprogrammet på Raspberry Pi. Ange kommandot:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Följande exempel på utdata är ett exempel på utdata som du ser i Kommandotolken på Raspberry Pi:

![Utdata från Raspberry Pi-app][img-raspberry-output]

Tryck på **Ctrl-C** att avsluta programmet när som helst.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Nästa steg

Gå till den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) för fler exempel och dokumentation om Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
