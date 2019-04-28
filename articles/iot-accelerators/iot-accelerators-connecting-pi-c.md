---
title: Etablera Raspberry Pi till fjärrövervakning med hjälp av C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en Raspberry Pi-enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som är skrivna i C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61454514"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Anslut Raspberry Pi enheten till lösningsacceleratorn för fjärrövervakning (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning. Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhetsprogram Raspberry Pi skrivna i C. I den här självstudien skapar du programmet på en Raspberry Pi med Raspbian-operativsystem.

Om du föredrar att simulera en enhet, se [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Maskinvara som krävs

En stationär dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

[Microsoft IoT-startpaket för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. Den här självstudien använder följande objekt från kit:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En Mini USB-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Nödvändig programvara för fjärrskrivbord

Du behöver SSH-klient på din stationära dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](https://www.putty.org/).
- De flesta Linux-distributioner och Mac OS innehåller SSH-kommandoradsverktyget. Mer information finns i [SSH med Linux- eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Programvara som krävs Raspberry Pi

Den här artikeln förutsätter att du har installerat den senaste versionen av den [Raspbian Operativsystemet på Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Följande steg visar hur du förbereder Raspberry Pi för att skapa ett C-program som ansluter till solution accelerator:

1. Ansluta till Raspberry Pi med **ssh**. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på den [Raspberry Pi webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. För att slutföra stegen i den här guiden följer du stegen i [konfigurera Linux-utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) att lägga till de nödvändiga utvecklingsverktygen och -bibliotek i Raspberry Pi.

## <a name="view-the-code"></a>Visa koden

Den [exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) används i den här guiden är tillgänglig i Azure IoT C SDK: er GitHub-lagringsplatsen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned källkoden och Förbered projektet

För att förbereda projektet, klona eller ladda ned den [databasen Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) från GitHub.

Du hittar exemplet i den **samples/lösningar/remote_monitoring_client** mapp.

Öppna den **remote_monitoring.c** fil i den **samples/lösningar/remote_monitoring_client** mapp i en textredigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Följande steg beskriver hur du använder *CMake* du skapar klientprogrammet. Fjärråtkomst övervakning klientprogrammet har skapats som en del av skapandeprocessen för SDK.

1. Redigera den **remote_monitoring.c** filen för att ersätta `<connectionstring>` med enhetens anslutningssträng som du antecknade i början av den här guiden när du har lagt till en enhet för solution accelerator.

1. Navigera till roten för din klonade kopia av den [databasen Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) databasen och kör följande kommandon för att skapa klientprogrammet:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klientprogrammet och skickar telemetri till IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till solution accelerator.
    - Svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
