---
title: Skapa en IoT Plug and Play förhands gransknings enhet (Windows) | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden och se till att enheten är ansluten till IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3d0a054e587a8f067be33913b05d2962b970fb1a
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321621"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Snabb start: Använd en enhets kapacitets modell för att skapa en IoT Plug and Play förhands gransknings enhet (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en IoT plug and Play-enhet. Ett DCM är ofta kopplat till en produkt-SKU. Funktionerna som definieras i DCM är indelade i återanvändbara gränssnitt. Du kan generera Skeleton-enhets kod från ett DCM-kort. Den här snabb starten visar hur du använder VS Code i Windows för att skapa en IoT Plug and Play-enhet med hjälp av ett DCM.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten måste du installera följande program vara på den lokala datorn:

* [Bygg verktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med **C++ build-verktyg** och **NuGet Package Manager-komponentens** arbets belastningar. Eller om du redan har [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbets belastningar installerade.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Använd följande steg för att installera [Azure IoT-verktyg för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter **Azure IoT-verktyg**.
1. Välj **installera**.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databas](https://github.com/Azure/azure-iot-explorer/releases) sida genom att välja. msi-filen under "till gångar" för den senaste uppdateringen.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutnings strängen för din företags modell databas

Du hittar _anslutnings strängen för din företags modell databas_ i [Azure-certifierad för IoT Portal](https://preview.catalog.azureiotsolutions.com) -portalen när du loggar in med ett arbets-eller skol konto i Microsoft, eller ditt Microsoft partner-ID om du har ett. När du har loggat in väljer du **företags databas** och sedan **anslutnings strängar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten använder du [Vcpkg](https://github.com/microsoft/vcpkg) Library Manager för att installera Azure IoT C-enhetens SDK i utvecklings miljön.

1. Öppna en kommandotolk. Kör följande kommando för att installera Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Om du vill koppla samman den globala [integrationen](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)kör du följande (Obs! kräver administratör vid första användningen):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installera Azure IoT C-Vcpkg för enhets-SDK:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Redigera din modell

I den här snabb starten använder du en befintlig funktions modell för exempel enheter och tillhör ande gränssnitt.

1. Skapa en `pnp_app` mapp på den lokala enheten. Du använder den här mappen för enhets modellens filer och enhets koden stub.

1. Ladda [ned exempel](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/EnvironmentalSensor.interface.json) [filen med enhets kapacitets modellen och gränssnittet](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/SampleDevice.model.json) och spara filerna i `pnp_app` mappen.

    > [!TIP]
    > Om du vill ladda ned en fil från GitHub navigerar du till filen, högerklickar på **RAW**och väljer sedan **Spara länk som**.

1. Öppna en `pnp_app` mapp med vs Code. Du kan visa filerna med IntelliSense:

    ![Enhets kapacitets modell](media/quickstart-create-pnp-device/dcm.png)

1. Ersätt `<YOUR_COMPANY_NAME_HERE>` i `@id` `schema` fälten och med ett unikt värde i filerna som du laddade ned. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu när du har ett DCM och tillhör ande gränssnitt kan du generera den enhets kod som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. När `pnp_app` mappen är öppen i vs Code, använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT-Plug and Play CodeGen CLI tar det några sekunder att ladda ned och installera automatiskt.

1. Välj den **SampleDevice.capabilitymodel.jspå** filen som ska användas för att generera enhets kod stub.

1. Ange projekt namnet **sample_device**. Detta är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **Via IoT Hub enhets anslutnings sträng** som anslutnings metod.

1. Välj **cmake-projekt i Windows** som projekt mal len.

1. Välj **via Vcpkg** som metod för att inkludera enhets-SDK: n.

1. En ny mapp med namnet **sample_device** skapas på samma plats som DCM-filen och i den genereras stub-filer för enhets koder. VS Code öppnar ett nytt fönster för att visa dessa.
    ![Enhets kod](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du kan använda Vcpkg-paketet för att skapa en genererad enhets kod stub. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en under `cmake` katalog i `sample_device` mappen och navigera till mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade koden stub (Ersätt plats hållaren med katalogen för din Vcpkg-lagrings platsen):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de build-verktyg som du använder:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Om cmake inte kan hitta din C++-kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

1. När skapandet har slutförts kör du programmet och skickar IoT Hub-enhetens anslutnings sträng som en parameter.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Enhets programmet börjar skicka data till IoT Hub.

    ![Enhets program som körs](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhets modell filer till modell databasen

Om du vill validera enhets koden med **Azure IoT Explorer**måste du publicera filerna på modell lagrings platsen.

1. När `pnp_app` mappen är öppen i vs Code, använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver och väljer **IoT plug-& Play: skicka filer till modell databasen**.

1. Välj `SampleDevice.capabilitymodel.json` och `EnvironmentalSensor.interface.json` filer.

1. Ange din anslutnings sträng för företags modellens databas.

    > [!NOTE]
    > Anslutnings strängen krävs bara första gången du ansluter till lagrings platsen.

1. I VS Code output-fönster och meddelande kan du kontrol lera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhets modellens filer kan du prova att använda kommandot **IoT plug and Play: Logga ut modell databasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill lägga till företags databasen väljer du **Inställningar**och sedan **+ Lägg till modulens definitions källa**, sedan **företags databasen**. Lägg till din anslutnings sträng för företags modell databas och välj **Spara och Anslut**.

1. På sidan **enhets** Översikt hittar du enhets identiteten som du skapade tidigare. När enhets programmet fortfarande körs i kommando tolken kontrollerar du att enhetens **anslutnings status** i Azure IoT Explorer rapporterar som _ansluten_ (om inte klickar du på **Uppdatera** tills den är). Välj enheten om du vill visa mer information.

1. Expandera gränssnittet med ID **urn: <YOUR_INTERFACE_NAME>: EnvironmentalSensor: 1** om du vill se IoT plug and Play primitiver – egenskaper, kommandon och telemetri. Gränssnitts namnet som visas är det namn som du anger i när du skapade din modell.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en IoT Plug and Play-enhet med hjälp av ett DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudie: skapa och testa en enhets kapacitets modell med Visual Studio Code](tutorial-pnp-visual-studio-code.md)
