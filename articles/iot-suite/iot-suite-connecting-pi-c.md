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
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 45f4de7e9ec880775f9ccf77b7d945766d465aa7
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
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

Följande steg visar hur du förbereder din hallon Pi för att bygga ett C-program som ansluter till den förkonfigurerade lösningen:

1. Anslut till din hallon Pi med `ssh`. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på den [hallon Pi webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera din hallon Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommando för att lägga till nödvändiga utvecklingsverktyg och bibliotek hallon-Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. Använd följande kommandon för att installera klientbibliotek IoT-hubb:

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. Klona Parson JSON-parsern för att din hallon Pi med följande kommandon:

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>Skapa ett projekt

Utför följande steg med hjälp av den `ssh` anslutning till din hallon Pi:

1. Skapa en mapp med namnet `remote_monitoring` i arbetsmappen på hallon Pi. Navigera till den här mappen i kommandoraden:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Skapa fyra filerna `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, och `CMakeLists.txt` i den `remote_monitoring` mapp.

1. Skapa mapp med namnet `parson` i den `remote_monitoring` mapp.

1. Kopiera filerna `parson.c` och `parson.h` från den lokala kopian av Parson databasen till den `remote_monitoring/parson` mapp.

1. I en textredigerare, öppnar den `remote_monitoring.c` filen. På hallon Pi, kan du använda antingen den `nano` eller `vi` textredigerare. Lägg till följande `#include`-uttryck:

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

## <a name="add-code-to-run-the-app"></a>Lägg till kod för att köra appen

I en textredigerare, öppnar den `remote_monitoring.h` filen. Lägg till följande kod:

```c
void remote_monitoring_run(void);
```

I en textredigerare, öppnar den `main.c` filen. Lägg till följande kod:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
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
