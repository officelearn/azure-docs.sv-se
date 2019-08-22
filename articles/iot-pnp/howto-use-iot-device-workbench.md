---
title: Använd Azure IoT Device Workbench för att bygga IoT Plug and Play Preview-enheter | Microsoft Docs
description: Använd Azure IoT Device Workbench-tillägget i Visual Studio Code för att påskynda redigeringen av IoT Plug and Play enhets modeller och implementera enhets koden.
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eaf1e313e3f88e151576ff00aec762a5fc2d8c66
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880493"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>Använda Azure IoT Device Workbench-tillägget i Visual Studio Code

Kod tillägget för Azure IoT Device Workbench Visual Studio ger en integrerad miljö för att redigera enhets kapacitets modeller (DCM) och gränssnitt, publicera på modell databaser och generera Skeleton C-kod för att implementera enhets programmet.

Den här artikeln visar hur du:

- Generera enhets kod och program projekt.
- Använd den genererade koden i enhets projektet.
- Iterera genom att återskapa Skeleton-koden.

Mer information om hur du använder enhets Workbench tillägget för att utveckla IoT- [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)enheter finns i.

## <a name="prerequisites"></a>Förutsättningar

Installera [Visual Studio Code](https://code.visualstudio.com/).

Använd följande steg för att installera tillägget i VS Code.

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter och installera **Azure IoT Device Workbench** från Marketplace.

## <a name="generate-device-code-and-project"></a>Generera enhets kod och projekt

I VS Code använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub** för att konfigurera Skeleton-koden och projekt typen. I följande lista beskrivs varje steg i detalj:

- **DCM-fil som ska användas för att generera koden**. Om du vill generera enhets koden Skeleton öppnar du mappen som innehåller de DCM-och gränssnitts filer som implementeras. Om kod generatorn inte kan hitta ett gränssnitt som DCM kräver, laddar det ned det från modell lagringen efter behov.

- **Enhets kod språk**. För närvarande stöder kod generatorn bara ANSI C.

- **Projekt namn**. Projekt namnet används som mappnamn för den genererade koden och andra projektfiler. Mappen är som standard placerad bredvid DCM-filen. För att undvika att manuellt kopiera den genererade kodmodulen när du uppdaterar din DCM och återskapa enhets koden, behåller du DCM-filen i samma mapp som projektmappen.

- **Projekt typ**. Kod generatorn genererar också en projekt fil så att du kan integrera koden i ditt eget projekt eller i enhetens SDK-projekt. För närvarande är de projekt typer som stöds:

    - **Cmake-projekt**: för ett enhets projekt som använder [cmake](https://cmake.org/) som build-system. Med det här alternativet `CMakeLists.txt` skapas en fil i samma mapp som C-koden.
    - **MXChip IoT DevKit-projekt**: för ett enhets projekt som körs på en [MXChip IoT DevKit](https://aka.ms/iot-devkit) -enhet. Det här alternativet genererar ett Arduino-projekt som du kan [använda i vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) eller i Arduino IDE för att skapa och köra på en IoT DevKit-enhet.

- **Metod för att ansluta till Azure IoT**. De genererade filerna innehåller också kod som konfigurerar enheten för att ansluta till Azure IoT Hub. Du kan välja att ansluta direkt till [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) eller använda [enhets etablerings tjänsten](https://docs.microsoft.com/azure/iot-dps).

    - **Via IoT Hub enhets anslutnings sträng**: Ange enhets anslutnings strängen som enhets programmet ska ansluta till IoT Hub direkt.
    - **Via den symmetriska DPS-nyckeln**: Ange omfångs **-ID**, **registrerings-ID**och **SAS-nyckel** för det enhets program som krävs för att ansluta till IoT Hub eller IoT Central med hjälp av DPS.

Kod generatorn försöker använda DCM-och Interface-filer som finns i den lokala mappen. Om-gränssnittets filer inte finns i den lokala mappen söker kod generatorn efter dem i den offentliga modell databasen eller i företags modellens lagrings plats. [Vanliga gränssnitts filer](./concepts-common-interfaces.md) lagras i den offentliga modellens lagrings plats.

När kodgenerering är klar öppnas ett nytt VS Code-fönster med koden i tillägget. Om du öppnar en genererad fil, till exempel **main. c**, kan det hända att IntelliSense-rapporter inte kan öppna källfilerna för c SDK. Om du vill aktivera rätt IntelliSense-och kod navigering använder du följande steg för att inkludera C SDK-källan:

1. I VS Code använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver **och väljerC++C/: Redigera konfigurationer (JSON)** för att öppna filen **c_cpp_properties. JSON** .

1. Lägg till sökvägen till enhets-SDK: `includePath` n i avsnittet:

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

För att bygga enhets koden tillsammans med Device C SDK med CMake i en Linux-miljö, till exempel Ubuntu eller Debian:

1. Öppna ett Terminal-program.

1. Installera **gcc**, **git**, `cmake`och alla beroenden med `apt-get` kommandot:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrol lera att versionen `cmake` av är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -lagringsplatsen:

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Kopiera mappen som innehåller den genererade koden till rotmappen för enhetens SDK.

1. I vs Code öppnar du `CMakeLists.txt` filen i rotmappen för enhetens SDK.

1. Lägg till följande rad i slutet av `CMakeLists.txt` filen för att inkludera enhets koden stub-mapp när du kompilerar SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Skapa en mapp som `cmake` heter i rotmappen för enhetens SDK och navigera till mappen.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enhets-SDK och den genererade koden stub:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

För att bygga enhets koden tillsammans med enhet C SDK i Windows med CMake och Visual Studio C/C++ compilers på kommando raden, se [IoT plug and Play snabb start](./quickstart-create-pnp-device.md). Följande steg visar hur du skapar enhets koden tillsammans med enhet C SDK som CMake-projekt i Visual Studio.

1. Installera [Visual Studio 2019 (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++**  arbets belastning.

1. Öppna Visual Studio och välj **klona eller checka ut kod**på sidan **Kom igång** :

1. Klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -lagringsplatsen på **lagrings platsen**:

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Du bör förvänta dig att åtgärden tar flera minuter att slutföra. du kan se förloppet i **team Explorer** -fönstret.

1. Öppna **Azure-IoT-SDK – c-** lagringsplats i **team Explorer**, Välj **grenar**, Sök efter **Public-Preview** -gren och checka ut den.

    ![Offentlig granskning](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. Kopiera mappen som innehåller den genererade koden till rotmappen för enhetens SDK.

1. `azure-iot-sdk-c` Öppna mappen i vs.

1. `CMakeLists.txt` Öppna filen i rotmappen för enhetens SDK.

1. Lägg till följande rad i slutet av `CMakeLists.txt` filen för att inkludera enhets koden stub-mapp när du kompilerar SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Leta upp List rutan **konfigurationer** i verktygsfältet **Allmänt** . Välj **hantera konfiguration** för att lägga till cmake-inställningen för projektet.

    ![Hantera konfiguration](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. I **cmake-inställningarna**lägger du till en ny konfiguration och väljer **x64-Frisläpp** som mål.

1. Lägg till följande rad i **kommando argumenten för cmake**:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Spara filen.

1. I **Solution Explorer**högerklickar du på `CMakeLists.txt` i rotmappen och väljer **skapa** på snabb menyn för att skapa enhets-SDK: n och den genererade koden stub.

1. När versionen har slutförts kör du programmet i kommando tolken och anger anslutnings strängen för IoT Hub enhet som en parameter.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Mer information om hur du använder CMake i Visual Studio finns i [build cmake Project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Följande steg visar hur du skapar enhets koden tillsammans med Device C SDK på macOS med CMake:

1. Öppna Terminal-program.

1. Använd [homebrew](https://homebrew.sh) för att installera alla beroenden:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Kontrol lera att [cmake](https://cmake.org/) är minst version **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Korrigera svängen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) till den senaste versionen som är tillgänglig.

1. Klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -lagringsplatsen:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Kopiera mappen som innehåller den genererade koden till rotmappen för enhetens SDK.

1. I vs Code öppnar du `CMakeLists.txt` filen i rotmappen för enhetens SDK.

1. Lägg till följande rad i slutet av `CMakeLists.txt` filen för att inkludera enhets koden stub-mapp när du kompilerar SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Skapa en mapp som `cmake` heter i rotmappen för enhetens SDK och navigera till mappen.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att använda CMake för att skapa enhets-SDK och den genererade koden stub:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterera genom att återskapa Skeleton-koden

Kod generatorn kan återskapa koden om du uppdaterar dina DCM-eller gränssnitts filer. Förutsatt att du redan har genererat enhets koden från en DCM-fil för att återskapa koden:

1. När mappen med DCM-filer är öppen använder du **Ctrl + Shift + P** för att öppna paletten kommando, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

1. Välj den DCM-fil som du uppdaterade.

1. Välj **Återskapa kod för {Project Name}** .

1. Kod generatorn använder den tidigare inställningen som du konfigurerade och återskapar koden. Den skriver dock inte över de filer som kan innehålla användar kod `main.c` som och. `{project_name}_impl.c`

> [!NOTE]
> Om du uppdaterar URN-ID: t i gränssnitts filen behandlas det som ett nytt gränssnitt. När du återskapar koden genererar kod generatorn kod för gränssnittet, men skriver inte över den ursprungliga `{project_name}_impl.c` filen i filen.

## <a name="problems-and-feedback"></a>Problem och feedback

Azure IoT Device Workbench-tillägget är ett projekt med öppen källkod på GitHub. För eventuella problem och funktions begär Anden kan du [skapa ett problem på GitHub](https://github.com/microsoft/vscode-iot-workbench/issues).

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du använder Azure IoT Device Workbench för att redigera DCM-och gränssnitts filer. Du har också lärt dig hur du skapar Skeleton C-kod för att implementera enhets programmet. Ett förslag till nästa steg är att lära dig hur du [installerar och använder Azure IoT Explorer](./howto-install-iot-explorer.md) -verktyget.
