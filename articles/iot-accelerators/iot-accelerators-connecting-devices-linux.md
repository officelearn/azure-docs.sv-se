---
title: Etablera Linux-enheter till fjärrövervakning i C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som skrivits i C som körs på Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: fcc11347d06cde43c79ef4272f5c2ad87555c040
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734496"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en fysisk enhet till lösningsacceleratorn för fjärrövervakning.

Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhet-programmet skrivna i C. I den här självstudien skapar du program på en dator som kör Ubuntu (Linux).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här guiden behöver du en enhet som kör Ubuntu version 15.04 eller senare. Innan du fortsätter [konfigurera Linux-utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Visa koden

Den [exempelkoden](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring) används i den här guiden är tillgänglig i Azure IoT C SDK: er GitHub-lagringsplatsen.

### <a name="download-the-source-code-and-prepare-the-project"></a>Ladda ned källkoden och Förbered projektet

För att förbereda projektet, klona eller ladda ned den [databasen Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) från GitHub.

Du hittar exemplet i den **samples/lösningar/remote_monitoring** mapp.

Öppna den **remote_monitoring.c** fil i den **samples/lösningar/remote_monitoring** mapp i en textredigerare.

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
    ./samples/solutions/remote_monitoring/remote_monitoring_client
    ```

    Konsolen visar meddelanden som:

    - Programmet skickar exempel telemetri till solution accelerator.
    - Svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
