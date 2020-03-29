---
title: Etablera Linux-enheter för fjärrövervakning i C - Azure | Microsoft-dokument
description: Beskriver hur du ansluter en enhet till lösningsacceleratorn för fjärrövervakning med hjälp av ett program skrivet i C som körs på Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454510"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning.

Som med de flesta inbäddade program som körs på begränsade enheter skrivs klientkoden för enhetsprogrammet i C. I den här självstudien bygger du programmet på en dator som kör Ubuntu (Linux).

Om du föredrar att simulera en enhet läser du [Skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här guiden behöver du en enhet som kör Ubuntu version 15.04 eller senare. Innan du fortsätter [konfigurerar du din Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
