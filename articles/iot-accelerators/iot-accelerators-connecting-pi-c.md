---
title: Etablera Raspberry Pi till fjärrövervakning med hjälp av C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en Raspberry Pi-enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som är skrivna i C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 23e84a8d577bb1c4950de3acd76b0f8528551ae0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611449"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Anslut Raspberry Pi enheten till lösningsacceleratorn för fjärrövervakning (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en fysisk enhet till lösningsacceleratorn för fjärrövervakning. Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhetsprogram Raspberry Pi skrivna i C. I den här självstudien skapar du programmet på en Raspberry Pi med Raspbian-operativsystem.

### <a name="required-hardware"></a>Maskinvara som krävs

En stationär dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

[Microsoft IoT-startpaket för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. Den här självstudien använder följande objekt från kit:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En Mini USB-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Nödvändig programvara för fjärrskrivbord

Du behöver SSH-klient på din stationära dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](http://www.putty.org/).
- De flesta Linux-distributioner och Mac OS innehåller SSH-kommandoradsverktyget. Mer information finns i [SSH med Linux- eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Programvara som krävs Raspberry Pi

Den här artikeln förutsätter att du har installerat den senaste versionen av den [Raspbian Operativsystemet på Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Följande steg visar hur du förbereder Raspberry Pi för att skapa ett C-program som ansluter till solution accelerator:

1. Ansluta till Raspberry Pi med **ssh**. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på den [Raspberry Pi webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommando för att lägga till de nödvändiga utvecklingsverktygen och -bibliotek i Raspberry Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Använd följande kommandon för att hämta, skapa och installera IoT Hub-klientbibliotek på Raspberry Pi:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    mkdir cmake
    cd cmake
    cmake ..
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Skapa ett projekt

Utför följande steg med hjälp av den **ssh** anslutning till Raspberry Pi:

1. Skapa en mapp med namnet `remote_monitoring` i arbetsmappen på Raspberry Pi. Gå till den här mappen i ditt gränssnitt:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Skapa fyra filerna **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, och **CMakeLists.txt** i den `remote_monitoring` mappen.

1. I en textredigerare och öppna den **remote_monitoring.c** fil. På Raspberry Pi, kan du använda antingen den **nano** eller **vi** textredigerare. Lägg till följande `#include`-uttryck:

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

Spara den **remote_monitoring.c** filen och avsluta redigeraren.

## <a name="add-code-to-run-the-app"></a>Lägg till kod för att köra appen

I en textredigerare och öppna den **remote_monitoring.h** fil. Lägg till följande kod:

```c
void remote_monitoring_run(void);
```

Spara den **remote_monitoring.h** filen och avsluta redigeraren.

I en textredigerare och öppna den **main.c** fil. Lägg till följande kod:

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

Följande steg beskriver hur du använder *CMake* att bygga ditt klientprogram.

1. I en textredigerare och öppna den **CMakeLists.txt** fil i den `remote_monitoring` mapp.

1. Lägg till följande instruktioner för att definiera hur du skapar klientprogrammet:

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
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. Spara den **CMakeLists.txt** filen och avsluta redigeraren.

1. I den `remote_monitoring` mappen skapar du en mapp för att lagra den *gör* filer som CMake genererar. Kör sedan den **cmake** och **gör** kommandon på följande sätt:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klientprogrammet och skickar telemetri till IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
