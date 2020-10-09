---
title: Etablera Raspberry Pi till fjärrövervakning med C-Azure | Microsoft Docs
description: Beskriver hur du ansluter en Raspberry Pi-enhet till lösnings Accelerator för fjärrövervakning med hjälp av ett program som skrivits i C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454514"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Anslut din Raspberry Pi-enhet till lösnings acceleratorn för fjärr styrning (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du se hur du ansluter en riktig enhet till en lösnings Accelerator med fjärr styrning. Precis som med de flesta inbäddade program som körs på begränsade enheter skrivs klient koden för Raspberry Pi Device-programmet i C. I den här självstudien skapar du programmet på en Raspberry Pi som kör Raspbian OS.

Om du föredrar att simulera en enhet kan du läsa [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Nödvändig maskin vara

En stationär dator så att du kan fjärrans luta till kommando raden på Raspberry Pi.

[Microsoft IoT starter kit för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. I den här självstudien används följande objekt från paketet:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En USB-Mini-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Nödvändig Desktop-programvara

Du behöver SSH-klienten på din station ära dator så att du kan fjärrans luta till kommando raden på Raspberry Pi.

- Windows innehåller ingen SSH-klient. Vi rekommenderar att du använder [SparaTillFil](https://www.putty.org/).
- De flesta Linux-distributioner och Mac OS innehåller SSH-verktyget för kommando rads verktyg. Mer information finns i [SSH med Linux eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Nödvändig Raspberry Pi-programvara

Den här artikeln förutsätter att du har installerat den senaste versionen av [Raspbian OS på Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Följande steg visar hur du förbereder din Raspberry Pi för att skapa ett C-program som ansluter till Solution Accelerator:

1. Anslut till din Raspberry Pi med **SSH**. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på [Raspberry Pi-webbplatsen](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera din Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. För att slutföra stegen i den här instruktions guiden följer du stegen i [Konfigurera din Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att lägga till nödvändiga utvecklingsverktyg och bibliotek i Raspberry Pi.

## <a name="view-the-code"></a>Visa koden

[Exempel koden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) som används i den här guiden finns i GitHub-lagringsplatsen för Azure IoT C SDK: er.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned käll koden och Förbered projektet

Förbered projektet genom att klona eller ladda ned [Azure IoT C SDK-lagringsplatsen](https://github.com/Azure/azure-iot-sdk-c) från GitHub.

Exemplet finns i mappen **samples/Solutions/remote_monitoring_client** .

Öppna filen **remote_monitoring. c** i mappen **samples/Solutions/remote_monitoring_client** i en text redigerare.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Följande steg beskriver hur du använder *cmake* för att bygga klient programmet. Klient programmet för fjärr övervakning skapas som en del av build-processen för SDK.

1. Redigera filen **remote_monitoring. c** och Ersätt `<connectionstring>` med enhets anslutnings strängen som du antecknade i början av den här instruktions guiden när du lade till en enhet i Solution Accelerator.

1. Navigera till roten för din klonade kopia av lagrings platsen för [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) : n och kör följande kommandon för att skapa klient programmet:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klient programmet och skicka telemetri till IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till Solution Accelerator.
    - Svarar på metoder som anropas från instrument panelen för lösningen.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
