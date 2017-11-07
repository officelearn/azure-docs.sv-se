---
title: "Ansluter en enhet med hjälp av C på Linux | Microsoft Docs"
description: "Beskriver hur du ansluter en enhet till Azure IoT Suite förkonfigurerade fjärråtkomst övervakning lösningen med hjälp av ett program som skrivits i C som körs på Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Ansluta enheten till den fjärranslutna förkonfigurerade övervakningslösning (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Skapa och kör ett exempel på C-klienten Linux
Följande steg visar hur du skapar ett klientprogram som kommunicerar med fjärråtkomst övervakning förkonfigurerade lösningen. Det här programmet har skrivits i C och bygger och kör på Ubuntu Linux.

För att slutföra de här stegen behöver du en enhet som kör Ubuntu version 15.04 eller 15.10. Innan du fortsätter bör du installera de nödvändiga paketen på Ubuntu enheten med följande kommando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Installera klientbiblioteken på din enhet
Azure IoT Hub-klientbibliotek är tillgänglig som ett paket som du kan installera på din Ubuntu enheter med hjälp av den **lgh get** kommando. Utför följande steg för att installera det paket som innehåller IoT-hubb klienten bibliotek och huvudet filerna på din Ubuntu-dator:

1. Lägg till AzureIoT databasen till datorn i ett gränssnitt:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Installera paketet azure iot-sdk-c-utveckling
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Installera Parson JSON-parser
Klientbibliotek för IoT-hubb använda Parson JSON-parsern för att parsa meddelandet nyttolaster. Klona Parson GitHub-lagret med hjälp av följande kommando i en lämplig mapp på datorn:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Förbered ditt projekt
Skapa en mapp med namnet på datorn Ubuntu **remote\_övervakning**. I den **remote\_övervakning** mapp:

- Skapa fyra filerna **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h**, och **CMakeLists.txt**.
- Skapa mapp med namnet **parson**.

Kopiera filerna **parson.c** och **parson.h** från den lokala kopian av Parson databasen till den **remote\_övervakning/parson** mapp.

I en textredigerare, öppnar den **remote\_monitoring.c** fil. Lägg till följande `#include`-uttryck:
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Anropa fjärransluten\_övervakning\_köra funktionen
I en textredigerare, öppnar den **remote_monitoring.h** fil. Lägg till följande kod:

```
void remote_monitoring_run(void);
```

I en textredigerare, öppnar den **main.c** fil. Lägg till följande kod:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Skapa och kör appen
Följande steg beskriver hur du använder *CMake* att skapa ditt klientprogram.

1. I en textredigerare, öppnar den **CMakeLists.txt** filen i den **remote_monitoring** mapp.

1. Lägg till följande instruktioner för att definiera hur du skapar ditt klientprogram:
   
    ```
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
1. I den **remote_monitoring** mapp, skapa en mapp för att lagra den *Se* filer som CMake genererar och kör sedan den **cmake** och **Se** följande kommandon:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Kör klientprogrammet och skicka telemetri till IoT-hubb:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

