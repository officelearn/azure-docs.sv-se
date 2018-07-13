---
title: Etablera Linux-enheter till fjärrövervakning i C – Azure | Microsoft Docs
description: Beskriver hur du ansluter en enhet till den lösningsacceleratorn för fjärrövervakning använder ett program som skrivits i C som körs på Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 5d7d6522dc663f13ce40cc638ba90ac4043d435c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611420"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Anslut enheten till lösningsacceleratorn för fjärrövervakning (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en fysisk enhet till lösningsacceleratorn för fjärrövervakning.

## <a name="create-a-c-client-project-on-linux"></a>Skapa ett projekt för C-klienten på Linux

Precis som med de flesta embedded-program som körs på begränsade enheter, är klientkod för enhet-programmet skrivna i C. I den här självstudien skapar du program på en dator som kör Ubuntu (Linux).

För att slutföra dessa steg behöver du en enhet som kör Ubuntu version 15.04 eller senare. Innan du fortsätter att installera de nödvändiga paketen på Ubuntu-enheten med följande kommando:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Installera klientbiblioteken på din enhet

Klientbibliotek för Azure IoT Hub är tillgänglig som ett paket som du kan installera på din Ubuntu enheter med hjälp av den **apt-get** kommando. Utför följande steg för att installera det paket som innehåller de IoT Hub-biblioteket och huvud klientfilerna på Ubuntu-dator:

1. Lägg till AzureIoT lagringsplatsen till din dator i ett gränssnitt:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Installera azure-iot-sdk-c-dev-paket

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Installera Parson JSON-parser

Klientbibliotek för IoT Hub använder Parson JSON-parsern för att parsa meddelandet nyttolaster. Klona Parson GitHub-lagringsplatsen med följande kommando i en lämplig mapp på datorn:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Förbered ditt projekt

Skapa en mapp med namnet på din dator med Ubuntu, `remote_monitoring`. I den `remote_monitoring` mapp:

- Skapa fyra filerna `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, och `CMakeLists.txt`.
- Skapa mapp med namnet `parson`.

Kopiera filerna `parson.c` och `parson.h` från din lokala kopia av lagringsplatsen Parson till den `remote_monitoring/parson` mapp.

I en textredigerare och öppna den `remote_monitoring.c` filen. Lägg till följande `#include`-uttryck:

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

## <a name="add-code-to-run-the-app"></a>Lägg till kod för att köra appen

I en textredigerare och öppna den `remote_monitoring.h` filen. Lägg till följande kod:

```c
void remote_monitoring_run(void);
```

I en textredigerare och öppna den `main.c` filen. Lägg till följande kod:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

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
