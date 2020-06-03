---
title: Använd Visual Studio och Visual Studio Code för att bygga IoT Plug and Play Preview-enheter | Microsoft Docs
description: Använd Visual Studio och Visual Studio Code för att påskynda redigeringen av IoT Plug and Play enhets modeller och implementera enhets koden.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 038d9ff39f388d1ef7b09b951c09dbe3420858b7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298235"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Använd Visual Studio och Visual Studio Code för att bygga IoT Plug and Play-enheter

Azure IoT Tools för Visual Studio Code tillhandahåller en integrerad miljö för att redigera enhets kapacitets modeller (DCM) och gränssnitt, publicera på modell databaser och generera Skeleton C-kod för att implementera enhets programmet.

Den här artikeln visar hur du:

- Generera enhets kod och program projekt.
- Använd den genererade koden i enhets projektet.
- Iterera genom att återskapa Skeleton-koden.

Mer information om att använda VS-koden för att utveckla IoT-enheter finns i [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench) .

## <a name="prerequisites"></a>Förutsättningar

Installera [Visual Studio Code](https://code.visualstudio.com/).

Använd följande steg för att installera tilläggs paketet i VS Code.

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter och installera **Azure IoT-verktyg** från Marketplace.

## <a name="generate-device-code-and-project"></a>Generera enhets kod och projekt

I VS Code använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub** för att konfigurera Skeleton-koden och projekt typen. I följande lista beskrivs varje steg i detalj:

- **DCM-fil som ska användas för att generera koden**. Om du vill generera enhets koden Skeleton öppnar du mappen som innehåller de DCM-och gränssnitts filer som implementeras. Om kod generatorn inte kan hitta ett gränssnitt som DCM kräver, laddar det ned det från modell lagringen efter behov.

- **Enhets kod språk**. För närvarande stöder kod generatorn bara ANSI C.

- **Projekt namn**. Projekt namnet används som mappnamn för den genererade koden och andra projektfiler. Mappen är som standard placerad bredvid DCM-filen. För att undvika att manuellt kopiera den genererade kodmodulen när du uppdaterar din DCM och återskapa enhets koden, behåller du DCM-filen i samma mapp som projektmappen.

- **Metod för att ansluta till Azure IoT**. De genererade filerna innehåller också kod som konfigurerar enheten för att ansluta till Azure IoT Hub. Du kan välja att ansluta direkt till [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) eller använda [enhets etablerings tjänsten](https://docs.microsoft.com/azure/iot-dps).

    - **Via IoT Hub enhets anslutnings sträng**: Ange enhets anslutnings strängen som enhets programmet ska ansluta till IoT Hub direkt.
    - **Via den symmetriska DPS-nyckeln**: ange **ID-omfånget**, **symmetrisk nyckel** och **enhets-ID** för enhets programmet som krävs för att ansluta till IoT Hub eller IoT Central med hjälp av DPS.

- **Projekt typ**. Kod generatorn genererar också ett CMake-eller Arduino-projekt. För närvarande är de projekt typer som stöds:

    - **Cmake-projekt i Windows**: för ett enhets projekt som använder [cmake](https://cmake.org/) som build-system i Windows. Det här alternativet genererar `CMakeLists.txt` med konfiguration av enhets-SDK i samma mapp som C-koden.
    - **Cmake-projekt i Linux**: för ett enhets projekt som använder [cmake](https://cmake.org/) som build-system på Linux. Det här alternativet genererar `CMakeLists.txt` med konfiguration av enhets-SDK i samma mapp som C-koden.
    - **MXChip IoT DevKit-projekt**: för ett enhets projekt som körs på en [MXChip IoT DevKit](https://aka.ms/iot-devkit) -enhet. Det här alternativet genererar ett Arduino-projekt som du kan [använda i vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) eller i Arduino IDE för att skapa och köra på en IoT DevKit-enhet.

- **Enhets-SDK-typ**. Om du väljer CMake som projekt typ är detta steget för att konfigurera hur genererad kod kommer att innehålla Azure IoT C-enhets-SDK i `CMakeLists.txt` :

    - **Via källkod**: den genererade koden förlitar sig på [enhetens SDK-källkod](https://github.com/Azure/azure-iot-sdk-c) för att inkludera i och utveckla tillsammans med den. Detta rekommenderas när du har anpassat käll koden för enhets-SDK: n.
    - **Via Vcpkg**: den genererade koden förlitar sig på [enhetens SDK-Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) för att inkludera i och utveckla tillsammans med den. Detta är det rekommenderade sättet för enheter som kör Windows, Linux eller macOS.

    > [!NOTE]
    > macOS-stöd för Azure IoT C-Vcpkg för enhets hantering pågår.

Kod generatorn försöker använda DCM-och Interface-filer som finns i den lokala mappen. Om-gränssnittets filer inte finns i den lokala mappen söker kod generatorn efter dem i den offentliga modell databasen eller i företags modellens lagrings plats. [Vanliga gränssnitts filer](./concepts-common-interfaces.md) lagras i den offentliga modellens lagrings plats.

När kodgenerering är klar öppnas ett nytt VS Code-fönster med koden i tillägget. Om du öppnar en genererad fil, till exempel **main. c**, kan det hända att IntelliSense-rapporter inte kan öppna källfilerna för c SDK. Om du vill aktivera rätt IntelliSense-och kod navigering använder du följande steg för att inkludera C SDK-källan:

1. I VS Code använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver och väljer **C/C++: redigera konfigurationer (JSON)** för att öppna **c_cpp_properties. JSON** -filen.

1. Lägg till sökvägen till enhets-SDK: n i `includePath` avsnittet:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Spara filen.

## <a name="use-the-generated-code"></a>Använd den genererade koden

I följande anvisningar beskrivs hur du använder den genererade koden i ditt eget enhets projekt på olika plattformar för utvecklings datorer. Anvisningarna förutsätter att du använder en IoT Hub enhets anslutnings sträng med den genererade koden:

### <a name="linux"></a>Linux

För att bygga enhets koden tillsammans med enhet C SDK-Vcpkg med CMake i en Linux-miljö, till exempel Ubuntu eller Debian:

1. Öppna ett Terminal-program.

1. Installera **gcc**, **git**, `cmake` och alla beroenden med `apt-get` kommandot:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

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

    Om du sedan vill koppla [samman hela användaren, kör du:](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)

    ```bash
    ./vcpkg integrate install
    ```

1. Installera Azure IoT C-Vcpkg för enhets-SDK:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Skapa en under `cmake` katalog i mappen som innehåller den genererade koden stub och navigera till mappen:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enhets-SDK och den genererade koden stub:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. När versionen har slutförts kör du programmet och anger anslutnings strängen för IoT Hub enhet som parameter.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

För att bygga enhets koden tillsammans med enhet C SDK i Windows med hjälp av CMake och Visual Studio C/C++-kompilatorn på kommando raden, se [IoT plug and Play snabb start](./quickstart-create-pnp-device-windows.md). Följande steg visar hur du skapar enhets koden tillsammans med enhet C SDK-Vcpkg som CMake-projekt i Visual Studio.

1. Följ stegen i [snabb](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) starten för att installera Azure IoT-enhetens SDK för C via Vcpkg.

1. Installera [Visual Studio 2019 (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++** -arbetsbelastning.

1. Öppna Visual Studio, Välj **fil > öppna > cmake...** för att öppna `CMakeLists.txt` i mappen innehåller genererad kod.

1. Leta upp List rutan **konfigurationer** i verktygsfältet **Allmänt** . Välj **hantera konfiguration** för att lägga till cmake-inställningen för projektet.

    ![Hantera konfiguration](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. I **cmake-inställningarna**lägger du till en ny konfiguration och väljer **x86-debug** som mål.

1. Lägg till följande rad i **kommando argumenten för cmake**:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Spara filen.

1. Växla till **x86-debug** i list rutan **konfigurationer** . Det behövs några sekunder för att CMake ska generera cacheminnet för den. Visa fönstret utdata om du vill se förloppet.

    ![CMake-utdata](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. I **Solution Explorer**högerklickar du på `CMakeLists.txt` i rotmappen och väljer **skapa** på snabb menyn för att skapa den genererade koden stub med enhets-SDK: n.

1. När versionen har slutförts kör du programmet i kommando tolken och anger anslutnings strängen för IoT Hub enhet som en parameter.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Mer information om hur du använder CMake i Visual Studio finns i [build cmake Project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Följande steg visar hur du skapar enhets koden tillsammans med käll koden för Device C SDK på macOS med CMake:

1. Öppna Terminal-program.

1. Använd [homebrew](https://brew.sh) för att installera alla beroenden:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Kontrol lera att [cmake](https://cmake.org/) är minst version **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Korrigera svängen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) till den senaste versionen som är tillgänglig.

1. I mappen som innehåller den genererade koden klonar du [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -lagringsplatsen:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en mapp `cmake` som heter under den mapp som innehåller den genererade koden och navigera till mappen.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enhets-SDK och den genererade koden stub:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. När versionen har slutförts kör du programmet och anger anslutnings strängen för IoT Hub enhet som parameter.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterera genom att återskapa Skeleton-koden

Kod generatorn kan återskapa koden om du uppdaterar dina DCM-eller gränssnitts filer. Förutsatt att du redan har genererat enhets koden från en DCM-fil för att återskapa koden:

1. När mappen med DCM-filer är öppen använder du **Ctrl + Shift + P** för att öppna paletten kommando, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

1. Välj den DCM-fil som du uppdaterade.

1. Välj **Återskapa kod för {Project Name}**.

1. Kod generatorn använder den tidigare inställningen som du konfigurerade och återskapar koden. Den skriver dock inte över de filer som kan innehålla användar kod som `main.c` och `{project_name}_impl.c` .

> [!NOTE]
> Om du uppdaterar URN-ID: t i gränssnitts filen behandlas det som ett nytt gränssnitt. När du återskapar koden genererar kod generatorn kod för gränssnittet, men skriver inte över den ursprungliga `{project_name}_impl.c` filen i filen.

## <a name="problems-and-feedback"></a>Problem och feedback

Azure IoT Tools är ett projekt med öppen källkod på GitHub. För eventuella problem och funktions begär Anden kan du [skapa ett problem på GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du använder Visual Studio och Visual Studio Code för att generera Skeleton C-kod för att implementera enhets programmet. Ett förslag till nästa steg är att lära dig hur du [installerar och använder Azure IoT Explorer](./howto-install-iot-explorer.md) -verktyget.
