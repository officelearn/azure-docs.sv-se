---
title: "Etablera hallon Pi till fjärråtkomst övervakning med hjälp av C - Azure | Microsoft Docs"
description: "Beskriver hur du ansluter en hallon Pi-enhet till Azure IoT Suite förkonfigurerade fjärråtkomst övervakning lösningen med hjälp av ett program som skrivits i C."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: dobett
ms.openlocfilehash: 7cfa6dd93c6db7477e03ff966b2ac8af15de3614
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Ansluta enheten hallon Pi till fjärråtkomst övervakning förkonfigurerade lösningen (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här kursen visar hur du ansluter en fysisk enhet till den fjärranslutna förkonfigurerade övervakningslösning. Precis som med de flesta embedded-program som körs på begränsad enheter, är klientkod för programmets hallon Pi enhet skriven i C. I den här självstudiekursen skapar du programmet på en hallon Pi Raspbian operativsystem.

### <a name="required-hardware"></a>Nödvändig maskinvara

En stationär dator så att du kan fjärransluta till kommandoraden på hallon Pi.

[Microsoft IoT startpaket för hallon Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. Den här kursen använder följande objekt från kit:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En Mini USB-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Nödvändig programvara för skrivbordet

Du måste SSH-klienten på den stationära datorn så att du kan fjärransluta till kommandoraden på hallon Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](http://www.putty.org/).
- De flesta distributioner för Linux och Mac OS inkluderar SSH-kommandoradsverktyget. Mer information finns i [SSH med Linux- eller Mac OS x](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Programvara som krävs hallon Pi

Den här artikeln förutsätter att du har installerat den senaste versionen av den [Raspbian OS på din hallon Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Följande steg visar hur du förbereder din hallon Pi för att bygga ett C-program som ansluter till den förkonfigurerade lösningen:

1. Anslut till din hallon Pi med **ssh**. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på den [hallon Pi webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera din hallon Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommando för att lägga till nödvändiga utvecklingsverktyg och bibliotek hallon-Pi:

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Använd följande kommandon för att hämta, bygga och installera klientbibliotek IoT-hubb på din hallon Pi:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Skapa ett projekt

Utför följande steg med hjälp av den **ssh** anslutning till din hallon Pi:

1. Skapa en mapp med namnet `remote_monitoring` i arbetsmappen på hallon Pi. Navigera till den här mappen i gränssnittet:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Skapa fyra filerna **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, och **CMakeLists.txt** i den `remote_monitoring` mapp.

1. I en textredigerare, öppnar den **remote_monitoring.c** fil. På hallon Pi, kan du använda antingen den **nano** eller **vi** textredigerare. Lägg till följande `#include`-uttryck:

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

Spara den **remote_monitoring.c** filen och avsluta redigeraren.

## <a name="add-code-to-run-the-app"></a>Lägg till kod för att köra appen

I en textredigerare, öppnar den **remote_monitoring.h** fil. Lägg till följande kod:

```c
void remote_monitoring_run(void);
```

Spara den **remote_monitoring.h** filen och avsluta redigeraren.

I en textredigerare, öppnar den **main.c** fil. Lägg till följande kod:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Spara den **main.c** filen och avsluta redigeraren.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Följande steg beskriver hur du använder *CMake* att skapa ditt klientprogram.

1. I en textredigerare, öppnar den **CMakeLists.txt** filen i den `remote_monitoring` mapp.

1. Lägg till följande instruktioner för att definiera hur du skapar ditt klientprogram:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. Spara den **CMakeLists.txt** filen och avsluta redigeraren.

1. I den `remote_monitoring` mapp, skapa en mapp för att lagra den *Se* filer som CMake genererar. Kör sedan den **cmake** och **Se** kommandon på följande sätt:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klientprogrammet och skicka telemetri till IoT-hubb:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
