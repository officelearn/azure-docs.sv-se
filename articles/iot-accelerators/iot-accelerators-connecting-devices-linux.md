---
title: Etablera Linux-enheter till fjärrövervakning i C-Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till lösningen för Fjärrövervaknings lösning med hjälp av ett program som skrivits i C och körs på Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454510"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Anslut enheten till Remote Monitoring Solution Accelerator (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du se hur du ansluter en riktig enhet till en lösnings Accelerator med fjärr styrning.

Precis som med de flesta inbäddade program som körs på begränsade enheter, skrivs klient koden för enhets programmet i C. I den här självstudien skapar du programmet på en dator som kör Ubuntu (Linux).

Om du föredrar att simulera en enhet kan du läsa [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här instruktions guiden behöver du en enhet som kör Ubuntu version 15,04 eller senare. Innan du fortsätter måste [du konfigurera din Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
