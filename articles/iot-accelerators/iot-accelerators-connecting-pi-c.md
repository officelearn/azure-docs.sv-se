---
title: Etablera Raspberry Pi till fjärrövervakning med C - Azure | Microsoft-dokument
description: Beskriver hur du ansluter en Raspberry Pi-enhet till lösningsacceleratorn för fjärrövervakning med hjälp av ett program skrivet i C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454514"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Anslut Raspberry Pi-enheten till lösningsacceleratorn för fjärrövervakning (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning. Som med de flesta inbäddade program som körs på begränsade enheter skrivs klientkoden för Raspberry Pi-enhetsprogrammet i C. I den här självstudien bygger du programmet på en Raspberry Pi som kör Raspbian OS.

Om du föredrar att simulera en enhet läser du [Skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Nödvändig maskinvara

En stationär dator som gör att du kan ansluta på distans till kommandoraden på Raspberry Pi.

[Microsoft IoT Starter Kit för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. Den här självstudien använder följande objekt från paketet:

- Hallon Pi 3
- MicroSD-kort (med NOOBS)
- En USB Mini-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Obligatorisk programvara för stationära datorer

Du behöver SSH-klienten på din stationära dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](https://www.putty.org/).
- De flesta Linux-distributioner och Mac OS inkluderar kommandoraden SSH-verktyget. Mer information finns i [SSH Använda Linux eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Krävs Raspberry Pi programvara

Den här artikeln förutsätter att du har installerat den senaste versionen av [Raspbian OS på din Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Följande steg visar hur du förbereder din Raspberry Pi för att skapa ett C-program som ansluter till lösningsacceleratorn:

1. Anslut till din Raspberry Pi med **ssh**. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på [Raspberry Pi:s webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. För att slutföra stegen i den här guiden följ stegen i [att konfigurera din Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att lägga till de utvecklingsverktyg och bibliotek som krävs i din Raspberry Pi.

## <a name="view-the-code"></a>Visa koden

[Exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) som används i den här guiden är tillgänglig i Azure IoT C SDKs GitHub-databasen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ner källkoden och förbered projektet

För att förbereda projektet, klona eller ladda ned [Azure IoT C SDKs-databasen](https://github.com/Azure/azure-iot-sdk-c) från GitHub.

Exemplet finns i mappen **samples/solutions/remote_monitoring_client.**

Öppna **filen remote_monitoring.c** i mappen **samples/solutions/remote_monitoring_client** i en textredigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Följande steg beskriver hur du använder *CMake* för att skapa klientprogrammet. Klientprogrammet för fjärrövervakning skapas som en del av byggprocessen för SDK.

1. Redigera **filen remote_monitoring.c** för att `<connectionstring>` ersätta med enhetsanslutningssträngen som du noterade i början av den här programvägledningen när du lade till en enhet i lösningsacceleratorn.

1. Navigera till roten för den klonade kopian av [Azure IoT C SDKs-databasen](https://github.com/Azure/azure-iot-sdk-c) och kör följande kommandon för att skapa klientprogrammet:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klientprogrammet och skicka telemetri till IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempeltelemetri till lösningsacceleratorn.
    - Svarar på metoder som anropas från lösningsinstrumentpanelen.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
