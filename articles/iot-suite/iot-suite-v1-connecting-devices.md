---
title: Anslut en enhet med C på Windows | Microsoft Docs
description: Beskriver hur du ansluter en enhet till Azure IoT Suite och förkonfigurerade fjärrövervakningslösningen med hjälp av ett program som skrivits i C som körs på Windows.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724017"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Anslut enheten till den förkonfigurerade lösningen för fjärrövervakning (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Skapa ett C exempellösningen på Windows
Följande steg visar hur du skapar ett klientprogram som kommunicerar med den förkonfigurerade lösningen för fjärrövervakning. Det här programmet har skrivits i C och bygger och kör på Windows.

Skapa ett Startprojekt i Visual Studio 2015 eller Visual Studio 2017 och Lägg till IoT Hub device klienten NuGet-paket:

1. I Visual Studio skapar du ett C-konsolprogram med Visual C++ **Win32-konsolprogrammet** mall. Ge projektet namnet **RMDevice**.
2. På den **programinställningar** sidan i den **guiden för Win32-program**, se till att **Konsolapp** är markerad och avmarkera **Precompiled rubriken** och **Security Development Lifecycle (SDL) kontrollerar**.
3. I **Solution Explorer**, ta bort filer stdafx.h, targetver.h och stdafx.cpp.
4. I **Solution Explorer**, byta namn på filen RMDevice.cpp till RMDevice.c.
5. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **hantera NuGet-paket**. Klicka på **Bläddra**, Sök sedan efter och installera följande NuGet-paket:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **egenskaper** att öppna projektets **egenskapssidor** dialogrutan. Mer information finns i [inställningen Visual C++-Projektegenskaper][lnk-c-project-properties]. 
7. Klicka på den **Linker** mapp, klicka sedan på den **indata** egenskapssidan.
8. Lägg till **crypt32.lib** till den **Additional Dependencies** egenskapen. Klicka på **OK** och sedan **OK** igen för att spara projektet egenskapsvärden.

Lägg till Parson JSON-biblioteket till det **RMDevice** projekt och Lägg till de nödvändiga `#include` instruktioner:

1. Klona Parson GitHub-lagringsplatsen med följande kommando i en lämplig mapp på datorn:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopiera filerna parson.h och parson.c från den lokala kopian av Parson lagringsplatsen till din **RMDevice** projektmapp.

1. I Visual Studio högerklickar du på den **RMDevice** projektet, klicka på **Lägg till**, och klicka sedan på **befintligt objekt**.

1. I den **Lägg till befintligt objekt** dialogrutan parson.h och parson.c filer i den **RMDevice** projektmapp. Klicka sedan på **Lägg till** att lägga till dessa två filer till ditt projekt.

1. Öppna filen RMDevice.c i Visual Studio. Ersätt de befintliga `#include` instruktioner med följande kod:
   
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
    > Nu kan du kontrollera att projektet har rätt beroenden konfigurera genom att skapa den.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Lägg till kod för att anropa den **remote\_övervakning\_kör** funktionen och sedan skapar och kör programmet på exempelenheten.

1. Ersätt den **huvudsakliga** funktionen med följande kod för att anropa den **remote\_övervakning\_kör** funktionen:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Klicka på **skapa** och sedan **skapa lösning** att bygga enhetprogram.

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet, klicka på **felsöka**, och klicka sedan på **Starta ny instans** att köra exemplet. Konsolen visar meddelanden som programmet skickar exempel telemetri till den förkonfigurerade lösningen tar emot önskade egenskapsvärden som angetts i instrumentpanelen för lösningen och svarar på metoderna som anropas från lösningens instrumentpanel.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
