---
title: "Etablera Windows-enheter till fjärrövervaknings i C - Azure | Microsoft Docs"
description: "Beskriver hur du ansluter en enhet till Azure IoT Suite förkonfigurerade fjärråtkomst övervakning lösningen med hjälp av ett program som skrivits i C som körs på Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2017
ms.author: dobett
ms.openlocfilehash: ef38517b55b352acf036e62d407f1ff840d6f804
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Ansluta enheten till den fjärranslutna förkonfigurerade övervakningslösning (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här kursen visar hur du ansluter en fysisk enhet till den fjärranslutna förkonfigurerade övervakningslösning.

## <a name="create-a-c-client-solution-on-windows"></a>Skapa en lösning för C-klienten i Windows

Precis som med de flesta embedded-program som körs på begränsad enheter, är klientkod för programmets enhet skriven i C. I den här självstudiekursen skapar du program på en dator som kör Windows.

### <a name="create-the-starter-project"></a>Skapa starter-projekt

Skapa ett starter-projekt i Visual Studio 2017 och Lägg till NuGet-paket IoT-hubb enheten klienten:

1. Skapa ett med hjälp av Visual C++ C-konsolprogram i Visual Studio **Windows-konsolprogram** mall. Namnge projektet **RMDevice**.

    ![Skapa Visual C++-Windows-konsolprogram](media/iot-suite-connecting-devices/visualstudio01.png)

1. I **Solution Explorer**, ta bort filerna `stdafx.h`, `targetver.h`, och `stdafx.cpp`.

1. I **Solution Explorer**, Byt namn på filen `RMDevice.cpp` till `RMDevice.c`.

    ![Solution Explorer visar byta namn på filen RMDevice.c](media/iot-suite-connecting-devices/visualstudio02.png)

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **hantera NuGet-paket**. Välj **Bläddra**, och Sök efter och installera följande NuGet-paket:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![NuGet-Pakethanteraren visar installerade Microsoft.Azure.IoTHub-paket](media/iot-suite-connecting-devices/visualstudio03.png)

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och välj sedan **egenskaper** att öppna projektets **egenskapssidor** i dialogrutan. Mer information finns i [Visual C++-projekt inställningsegenskaper](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Välj den **C/C++** mapp, Välj den **förkompilerat huvuden** egenskapssida.

1. Ange **förkompilerat huvud** till **inte använder förkompilerat huvuden**. Välj **tillämpa**.

    ![Projektegenskaperna Visa projekt som inte använder förkompilerade rubriker](media/iot-suite-connecting-devices/visualstudio04.png)

1. Välj den **Linker** mapp, Välj den **indata** egenskapssida.

1. Lägg till `crypt32.lib` till den **Additional Dependencies** egenskapen. Om du vill spara projektet egenskapsvärden, Välj **OK** och sedan **OK** igen.

    ![Projektegenskaperna visa Linker inklusive crypt32.lib](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Lägg till Parson JSON-biblioteket

Lägg till Parson JSON-biblioteket till det **RMDevice** projekt och Lägg till de nödvändiga `#include` instruktioner:

1. Klona Parson GitHub-lagret med hjälp av följande kommando i en lämplig mapp på datorn:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopiera den `parson.h` och `parson.c` filer från den lokala kopian av databasen Parson till din **RMDevice** projektmappen.

1. I Visual Studio högerklickar du på den **RMDevice** projekt, Välj **Lägg till**, och välj sedan **befintlig artikel**.

1. I den **Lägg till befintligt objekt** markerar den `parson.h` och `parson.c` filer i den **RMDevice** projektmappen. Om du vill lägga till de här två filerna i projektet, Välj **Lägg till**.

    ![Solution Explorer visar parson.h och parson.c filer](media/iot-suite-connecting-devices/visualstudio06.png)

1. Öppna i Visual Studio den `RMDevice.c` filen. Ersätta den befintliga `#include` instruktioner med följande kod:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Nu kan du kontrollera att projektet har rätt beroenden konfigurera genom att skapa lösningen.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exemplet

Lägg till kod för att anropa den **remote\_övervakning\_kör** fungera, och sedan skapa och köra programmet enhet:

1. Att anropa den **remote\_övervakning\_kör** fungerar genom att ersätta den **huvudsakliga** funktionen med följande kod:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Välj **skapa** och sedan **skapa lösning** att bygga enhetsprogram. Ignorera varningen om den **gmtime** funktion.

1. I **Solution Explorer**, högerklicka på den **RMDevice** projekt, Välj **felsöka**, och välj sedan **Starta ny instans** att köra exemplet . Konsolen visar meddelanden som:

    * Programmet skickar exempel telemetri till den förkonfigurerade lösningen.
    * Tar emot önskade egenskapsvärden som angetts i instrumentpanelen för lösningen.
    * Svarar metoderna som anropas från instrumentpanelen lösning.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
