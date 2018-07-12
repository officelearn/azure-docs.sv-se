---
title: Etablera Windows-enheter till fjärrövervakning i C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som skrivits i C som körs på Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309878"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en fysisk enhet till lösningsacceleratorn för fjärrövervakning.

## <a name="create-a-c-client-solution-on-windows"></a>Skapa en lösning för C-klienten på Windows

Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhet-programmet skrivna i C. I den här självstudien skapar du program på en dator som kör Windows.

### <a name="create-the-starter-project"></a>Skapa Startprojekt

Skapa ett Startprojekt i Visual Studio 2017 och Lägg till IoT Hub device klienten NuGet-paket:

1. I Visual Studio skapar du ett C-konsolprogram med Visual C++ **Windows-konsolprogram** mall. Ge projektet namnet **RMDevice**.

    ![Skapa Visual C++ Windows-konsolprogram](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. I **Solution Explorer**, ta bort filerna `stdafx.h`, `targetver.h`, och `stdafx.cpp`.

1. I **Solution Explorer**, byta namn på filen `RMDevice.cpp` till `RMDevice.c`.

    ![Solution Explorer visar RMDevice.c-fil som har fått nytt namn](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **hantera NuGet-paket**. Välj **Bläddra**, Sök sedan efter och installera följande NuGet-paket:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![NuGet-Pakethanteraren visar installerade Microsoft.Azure.IoTHub paket](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och välj sedan **egenskaper** att öppna projektets **egenskapssidor** dialogrutan. Mer information finns i [inställningen Visual C++-Projektegenskaper](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Välj den **C/C++** mapp, välj sedan den **förkompilerad version rubriker** egenskapssidan.

1. Ange **förkompilerad version rubrik** till **inte använder förkompilerad version rubriker**. Välj sedan **tillämpa**.

    ![Projektegenskaper Visa projekt som inte använder förkompilerade rubriker](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Välj den **Linker** mapp, välj sedan den **indata** egenskapssidan.

1. Lägg till `crypt32.lib` till den **Additional Dependencies** egenskapen. Om du vill spara projektet egenskapsvärden, Välj **OK** och sedan **OK** igen.

    ![Projektegenskaper visa Linker inklusive crypt32.lib](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Lägg till Parson JSON-biblioteket

Lägg till Parson JSON-biblioteket till det **RMDevice** projekt och Lägg till de nödvändiga `#include` instruktioner:

1. Klona Parson GitHub-lagringsplatsen med följande kommando i en lämplig mapp på datorn:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopiera den `parson.h` och `parson.c` filer från den lokala kopian av Parson lagringsplatsen till din **RMDevice** projektmapp.

1. I Visual Studio högerklickar du på den **RMDevice** projektet, Välj **Lägg till**, och välj sedan **befintligt objekt**.

1. I den **Lägg till befintligt objekt** dialogrutan den `parson.h` och `parson.c` filer i den **RMDevice** projektmapp. Om du vill lägga till dessa två filer i projektet, Välj **Lägg till**.

    ![Solution Explorer visar parson.h och parson.c filer](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. Visual Studio, öppna den `RMDevice.c` filen. Ersätt de befintliga `#include` instruktioner med följande kod:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > Nu kan du kontrollera att projektet har rätt beroenden konfigurera genom att skapa lösningen.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Lägg till kod för att anropa den **remote\_övervakning\_kör** fungerar, och sedan skapa och köra programmet på exempelenheten:

1. Att anropa den **remote\_övervakning\_kör** funktionen ersätter den **huvudsakliga** funktionen med följande kod:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Välj **skapa** och sedan **skapa lösning** att bygga enhetprogram.

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet, Välj **felsöka**, och välj sedan **Starta ny instans** att köra exemplet . Konsolen visar meddelanden som:

    * Programmet skickar exempel telemetri till solution accelerator.
    * Tar emot önskade egenskapsvärden som angetts i lösningens instrumentpanel.
    * Svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
