---
title: "Ansluter en enhet med hjälp av C för Windows | Microsoft Docs"
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Ansluta enheten till den fjärranslutna förkonfigurerade övervakningslösning (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Skapa en C exempellösning i Windows
Följande steg visar hur du skapar ett klientprogram som kommunicerar med fjärråtkomst övervakning förkonfigurerade lösningen. Det här programmet är skrivna i C och inbyggda och körs i Windows.

Skapa ett starter-projekt i Visual Studio 2015 eller Visual Studio 2017 och Lägg till NuGet-paket IoT-hubb enheten klienten:

1. Skapa ett med hjälp av Visual C++ C-konsolprogram i Visual Studio **Win32-konsolprogram** mall. Namnge projektet **RMDevice**.
2. På den **programinställningar** sidan i den **Win32 programguiden**, se till att **konsolprogrammet** är markerad och avmarkera **Precompiled huvudet** och **Security Development Lifecycle (SDL) kontrollerar**.
3. I **Solution Explorer**, ta bort filer stdafx.h, targetver.h och stdafx.cpp.
4. I **Solution Explorer**, Byt namn på filen RMDevice.cpp till RMDevice.c.
5. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **hantera NuGet-paket**. Klicka på **Bläddra**, och Sök efter och installera följande NuGet-paket:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. I **Solution Explorer**, högerklicka på den **RMDevice** projektet och klicka sedan på **egenskaper** att öppna projektets **egenskapssidor** i dialogrutan. Mer information finns i [Visual C++-projekt inställningsegenskaper][lnk-c-project-properties]. 
7. Klicka på den **Linker** mappen, klicka sedan på den **indata** egenskapssidan.
8. Lägg till **crypt32.lib** till den **Additional Dependencies** egenskapen. Klicka på **OK** och sedan **OK** igen för att spara projektet egenskapsvärden.

Lägg till Parson JSON-biblioteket till det **RMDevice** projekt och Lägg till de nödvändiga `#include` instruktioner:

1. Klona Parson GitHub-lagret med hjälp av följande kommando i en lämplig mapp på datorn:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopiera filerna parson.h och parson.c från den lokala kopian av databasen Parson till din **RMDevice** projektmappen.

1. I Visual Studio högerklickar du på den **RMDevice** projektet, klicka på **Lägg till**, och klicka sedan på **befintlig artikel**.

1. I den **Lägg till befintligt objekt** dialogrutan Välj parson.h och parson.c filer i den **RMDevice** projektmappen. Klicka på **Lägg till** att lägga till de här två filerna i projektet.

1. Öppna filen RMDevice.c i Visual Studio. Ersätta den befintliga `#include` instruktioner med följande kod:
   
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

## <a name="build-and-run-the-sample"></a>Skapa och köra exemplet

Lägg till kod för att anropa den **remote\_övervakning\_kör** fungera och sedan skapa och köra programmet för enheten.

1. Ersätt den **huvudsakliga** funktionen med följande kod för att anropa den **remote\_övervakning\_kör** funktionen:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Klicka på **skapa** och sedan **skapa lösning** att bygga enhetsprogram.

1. I **Solution Explorer**, högerklicka på den **RMDevice** projektet, klicka på **felsöka**, och klicka sedan på **Starta ny instans** att köra exemplet. Konsolen visar meddelanden som programmet skickar exempel telemetri till den förkonfigurerade lösningen, tar emot önskade egenskapsvärden i lösningen instrumentpanelen och svarar på metoderna som anropas från instrumentpanelen lösning.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
