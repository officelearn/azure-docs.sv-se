---
title: Använd Visual Studio- och Visual Studio-kod för att skapa IoT Plug and Play Preview-enheter | Microsoft-dokument
description: Använd Visual Studio- och Visual Studio-kod för att påskynda redigeringsmodellerna för IoT Plug and Play-enheter och implementera enhetskoden.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159242"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Använda Visual Studio- och Visual Studio-kod för att skapa IoT Plug and Play-enheter

Azure IoT Tools for Visual Studio Code tillhandahåller en integrerad miljö för att skapa enhetsfunktioner (DCM) och gränssnitt, publicera till modelldatabaser och generera skelett C-kod för att implementera enhetsprogrammet.

Den här artikeln visar hur du:

- Generera enhetskod och programprojekt.
- Använd den genererade koden i enhetsprojektet.
- Iterera genom att regenerera skelettkoden.

Mer information om hur du använder VS-koden [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)för att utveckla IoT-enheter finns i .

## <a name="prerequisites"></a>Krav

Installera [Visual Studio Code](https://code.visualstudio.com/).

Använd följande steg för att installera tilläggspaketet i VS-kod.

1. Välj fliken **Tillägg** i VS-kod.
1. Sök efter och installera **Azure IoT Tools** från marketplace.

## <a name="generate-device-code-and-project"></a>Generera enhetskod och projekt

I VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, anger **IoT Plug and Play**och väljer **Generera enhetskodsstub** för att konfigurera skelettkoden och projekttypen. I följande lista beskrivs varje steg i detalj:

- **DCM-fil som ska användas för att generera koden**. Om du vill generera programkoden för skelettet öppnar du mappen som innehåller DCM- och gränssnittsfilerna som implementeras. Om kodgeneratorn inte kan hitta ett gränssnitt som en DCM kräver hämtas den från modelldatabasen efter behov.

- **Enhetskodspråk**. För närvarande stöder kodgeneratorn endast ANSI C.

- **Projektnamn**. Projektnamnet används som mappnamn för den genererade koden och andra projektfiler. Mappen placeras som standard bredvid DCM-filen. Om du vill undvika att behöva kopiera den genererade kodmappen manuellt när du uppdaterar DCM:n och återskapar enhetskoden behåller du DCM-filen i samma mapp som projektmappen.

- **Metod för att ansluta till Azure IoT**. De genererade filerna innehåller också kod för att konfigurera enheten för att ansluta till Azure IoT Hub. Du kan välja att ansluta direkt till [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) eller använda [tjänsten Enhetsetablering](https://docs.microsoft.com/azure/iot-dps).

    - **Via IoT Hub-enhetsanslutningssträng:** ange enhetens anslutningssträng för enhetsprogrammet för att ansluta till IoT Hub direkt.
    - **Via DPS symmetrisk nyckel:** ange **ID-scope,** **symmetrisk nyckel** och **enhets-ID** för enhetsprogrammet som krävs för att ansluta till IoT Hub eller IoT Central med DPS.

- **Projekttyp**. Kodgeneratorn genererar också ett CMake- eller Arduino-projekt. För närvarande är de projekttyper som stöds:

    - **CMake Project på Windows:** för ett enhetsprojekt som använder [CMake](https://cmake.org/) som byggsystem i Windows. Det här `CMakeLists.txt` alternativet genererar med enhetSDK-konfigurationer i samma mapp som C-koden.
    - **CMake Project på Linux:** för ett enhetsprojekt som använder [CMake](https://cmake.org/) som byggsystem på Linux. Det här `CMakeLists.txt` alternativet genererar med enhetSDK-konfigurationer i samma mapp som C-koden.
    - **MXChip IoT DevKit-projektet:** för ett enhetsprojekt som körs på en [MXChip IoT DevKit-enhet.](https://aka.ms/iot-devkit) Det här alternativet genererar ett Arduino-projekt som du kan [använda i VS-kod](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) eller i Arduino IDE för att bygga och köra på en IoT DevKit-enhet.

- **Enhet SDK-typ**. Om du väljer CMake som projekttyp är det här steget för att konfigurera hur genererad kod ska inkludera Azure IoT C-enhet SDK i `CMakeLists.txt`:

    - **Via källkod:** den genererade koden förlitar sig på [enheten SDK källkod](https://github.com/Azure/azure-iot-sdk-c) att inkludera i och bygga tillsammans med den. Detta rekommenderas när du har anpassat enhetens SDK-källkod.
    - **Via Vcpkg**: den genererade koden förlitar sig på [enheten SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) att inkludera i och bygga tillsammans med den. Detta är det rekommenderade sättet för enheter som kör Windows, Linux eller macOS.

    > [!NOTE]
    > macOS-stöd för Azure IoT C-enhet SDK Vcpkg pågår.

Kodgeneratorn försöker använda DCM- och gränssnittsfiler som finns i den lokala mappen. Om gränssnittsfilerna inte finns i den lokala mappen letar kodgeneratorn efter dem i den offentliga modelldatabasen eller företagsmodelldatabasen. [Vanliga gränssnittsfiler](./concepts-common-interfaces.md) lagras i den offentliga modelldatabasen.

När kodgenereringen är klar öppnas ett nytt VS-kodfönster med koden. Om du öppnar en genererad fil, till exempel **main.c,** kanske du upptäcker att IntelliSense rapporterar att det inte kan öppna C SDK-källfilerna. Om du vill aktivera rätt IntelliSense- och kodnavigering följer du stegen nedan för att inkludera C SDK-källan:

1. I VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, skriver och väljer **C/C++: Redigera konfigurationer (JSON)** för att öppna **filen c_cpp_properties.json.**

1. Lägg till sökvägen till enhetenSDK i avsnittet: `includePath`

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Spara filen.

## <a name="use-the-generated-code"></a>Använd den genererade koden

Följande instruktioner beskriver hur du använder den genererade koden i ditt eget enhetsprojekt på olika utvecklingsmaskinplattformar. Instruktionerna förutsätter att du använder en IoT Hub-enhetsanslutningssträng med den genererade koden:

### <a name="linux"></a>Linux

Så här bygger du enhetskoden tillsammans med enheten C SDK Vcpkg med CMake i en Linux-miljö som Ubuntu eller Debian:

1. Öppna ett terminalprogram.

1. Installera **GCC,** **Git**och `cmake`alla beroenden med `apt-get` kommandot:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrollera att `cmake` versionen av är över **2.8.12** och versionen av **GCC** är över **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Installera Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Sedan, för att ansluta användaromfattande [integration](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md), kör:

    ```bash
    ./vcpkg integrate install
    ```

1. Installera Azure IoT C-enhet SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Skapa `cmake` en underkatalog i mappen innehåller den genererade kodsubten och navigera till den mappen:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enheten SDK och den genererade koden stub:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. När build lyckas kör du programmet som anger anslutningssträngen för IoT Hub-enheten som parameter.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Information om hur du skapar enhetskoden tillsammans med enheten C SDK i Windows med CMake och Visual Studio C/C++-kompilatorerna på kommandoraden finns i [snabbstarten IoT Plug and Play](./quickstart-create-pnp-device-windows.md). Följande steg visar hur du skapar enhetskoden tillsammans med enheten C SDK Vcpkg som CMake-projekt i Visual Studio.

1. Följ stegen i [snabbstarten](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) för att installera Azure IoT-enheten SDK för C via Vcpkg.

1. Installera [Visual Studio 2019 (Community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Komponenten NuGet package manager** och **skrivbordsutvecklingen med C++-arbetsbelastning.**

1. Öppna Visual Studio, välj **Arkiv > Öppna > CMake...** `CMakeLists.txt` för att öppna i mappen innehåller genererad kod.

1. Leta reda på listrutan Konfigurationer i **verktygsfältet** **Allmänt.** Välj **Hantera konfiguration** om du vill lägga till CMake-inställningen för projektet.

    ![Hantera konfiguration](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Lägg till en ny konfiguration i **CMake-inställningarna**och välj **x86-Debug** som mål.

1. Lägg till följande rad i **CMake-kommandoargument:**

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Spara filen.

1. Växla till **x86-Felsökning** i listrutan **Konfigurationer.** Den behöver några sekunder för att CMake ska kunna generera cachen för den. Visa utdatafönstret för att se förloppet.

    ![CMake-utgång](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Högerklicka på `CMakeLists.txt` rotmappen i **Lösningsutforskaren**och välj **Skapa** på snabbmenyn för att skapa den genererade kodsuben med enheten SDK.

1. När build lyckas kör du programmet som anger anslutningssträngen för IoT Hub-enheten som en parameter i kommandotolken.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Mer information om hur du använder CMake i Visual Studio finns i [Skapa CMake-projekt](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Följande steg visar hur du skapar enhetskoden tillsammans med enheten C SDK-källkoden på macOS med CMake:

1. Öppna terminalprogrammet.

1. Använd [Homebrew](https://homebrew.sh) för att installera alla beroenden:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Kontrollera att [CMake](https://cmake.org/) är minst version **2.8.12:**

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) till den senaste versionen tillgänglig.

1. Klona [Azure IoT C SDK-databasen](https://github.com/Azure/azure-iot-sdk-c) i mappen som innehåller den genererade koden:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en `cmake` mapp som anropas under mappen som innehåller den genererade koden och navigera till den mappen.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enheten SDK och den genererade koden stub:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. När build lyckas kör du programmet som anger anslutningssträngen för IoT Hub-enheten som parameter.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterera genom att regenerera skelettkoden

Kodgeneratorn kan återskapa koden om du uppdaterar dcm- eller gränssnittsfilerna. Förutsatt att du redan har genererat enhetskoden från en DCM-fil för att återskapa koden:

1. När mappen med DCM-filer är öppna använder du **Ctrl+Skift+P** för att öppna kommandopaletten, anger **IoT Plug and Play**och väljer **Generera Enhetskodsstub.**

1. Välj den DCM-fil som du uppdaterade.

1. Välj **Generera kod igen för {projektnamn}**.

1. Kodgeneratorn använder den tidigare inställningen som du har konfigurerat och återskapar koden. Den skriver dock inte över de filer som kan `main.c` innehålla `{project_name}_impl.c`användarkod, till exempel och .

> [!NOTE]
> Om du uppdaterar URN-id:n i gränssnittsfilen behandlas det som ett nytt gränssnitt. När du återskapar koden genererar kodgeneratorn kod för gränssnitt men skriver inte över `{project_name}_impl.c` den ursprungliga i filen.

## <a name="problems-and-feedback"></a>Problem och feedback

Azure IoT Tools är ett projekt med öppen källkod på GitHub. För eventuella problem och funktionsförfrågningar kan du [skapa ett problem på GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Visual Studio- och Visual Studio-koden för att generera en programkod för skelett C för att implementera enhetsprogrammet. Ett förslag på nästa steg är att lära sig hur du [installerar och använder Azure IoT explorer-verktyg.](./howto-install-iot-explorer.md)
