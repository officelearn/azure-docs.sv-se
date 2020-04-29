---
title: Skapa en Azure IoT Plug and Play Preview-enhet (Linux) | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden och se till att enheten är ansluten till IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550544"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snabb start: Använd en enhets kapacitets modell för att skapa en IoT Plug and Play Preview-enhet (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en IoT plug and Play-enhet. Ett DCM är ofta kopplat till en produkt-SKU. Funktionerna som definieras i DCM är indelade i återanvändbara gränssnitt. Du kan generera Skeleton-enhets kod från ett DCM-kort. Den här snabb starten visar hur du använder VS Code på Ubuntu Linux för att skapa en IoT Plug and Play-enhet med hjälp av DCM.

## <a name="prerequisites"></a>Krav

I den här snabb starten förutsätter vi att du använder Ubuntu Linux med en Skriv bords miljö. Stegen i den här självstudien har testats med Ubuntu 18,04.

För att slutföra den här snabb starten måste du installera följande program vara på den lokala Linux-datorn:

* Installera **gcc**, **git**, **cmake**och alla beroenden med `apt-get` kommandot:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrol lera att versionen `cmake` av är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Använd följande steg för att installera [Azure IoT-verktyg för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter **Azure IoT-verktyg**.
1. Välj **Installera**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutnings strängen för din företags modell databas

Du hittar _anslutnings strängen för din företags modell databas_ i [Azure-certifierad för IoT Portal](https://preview.catalog.azureiotsolutions.com) -portalen när du loggar in med ett arbets-eller skol konto i Microsoft, eller ditt Microsoft partner-ID om du har ett. När du har loggat in väljer du **företags databas** och sedan **anslutnings strängar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten använder du [Vcpkg](https://github.com/microsoft/vcpkg) Library Manager för att installera Azure IoT C-enhetens SDK i utvecklings miljön.

Öppna ett gränssnitt. Kör följande kommando för att installera Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="author-your-model"></a>Redigera din modell

I den här snabb starten använder du en befintlig funktions modell för exempel enheter och tillhör ande gränssnitt.

1. Skapa en `pnp_app` katalog på den lokala enheten. Du använder den här mappen för enhets modellens filer och enhets koden stub.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Hämta enhetens kapacitets modell och gränssnitts exempel filer `pnp_app` till mappen.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Öppna `pnp_app` en mapp med vs Code. Du kan visa filerna med IntelliSense:

    ![Enhets kapacitets modell](media/quickstart-create-pnp-device-linux/dcm.png)

1. Ersätt `<YOUR_COMPANY_NAME_HERE>` i fälten `@id` och `schema` med ett unikt värde i filerna som du laddade ned. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu när du har ett DCM och tillhör ande gränssnitt kan du generera den enhets kod som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. När `pnp_app` mappen är öppen i vs Code, använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator-verktyget tar det några sekunder att ladda ned och installera automatiskt.

1. Välj den **SampleDevice. capabilitymodel. JSON** -fil som ska användas för att generera enhets kod stub.

1. Ange projekt namnet **sample_device**. Detta är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **Via IoT Hub enhets anslutnings sträng** som anslutnings metod.

1. Välj **cmake-projekt på Linux** som projekt mal len.

1. Välj **via Vcpkg** som metod för att inkludera enhets-SDK: n.

1. En ny mapp med namnet **sample_device** skapas på samma plats som DCM-filen och i den genereras stub-filer för enhets koder. VS Code öppnar ett nytt fönster för att visa dessa.
    ![Enhets kod](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du kan använda enhets-SDK-källkoden för att skapa den genererade enhets koden stub. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en **cmake** -build-mapp för det **sample_device** programmet:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Kör CMake för att skapa din app med SDK. Följande kommando förutsätter att du har installerat **vcpkg** i din arbetsmapp:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. När skapandet har slutförts kör du ditt program och skickar IoT Hub-enhetens anslutnings sträng som parameter.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Enhets programmet börjar skicka data till IoT Hub.

    ![Enhets program som körs](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhets modell filer till modell databasen

Om du vill validera enhets koden med **AZ** CLI måste du publicera filerna på modell lagrings platsen.

1. När `pnp_app` mappen är öppen i vs Code, använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver och väljer **IoT plug-& Play: skicka filer till modell databasen**.

1. Välj `SampleDevice.capabilitymodel.json` och `EnvironmentalSensor.interface.json` filer.

1. Ange din anslutnings sträng för företags modellens databas.

    > [!NOTE]
    > Anslutnings strängen krävs bara första gången du ansluter till lagrings platsen.

1. I VS Code output-fönster och meddelande kan du kontrol lera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhets modellens filer kan du prova att använda kommandot **IoT plug and Play: Logga ut modell databasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Använd Azure IoT CLI för att validera koden

När enhets klient exemplet startar kan du kontrol lera att det fungerar med Azure CLI.

Använd följande kommando för att Visa telemetri som exempel enheten skickar. Du kan behöva vänta en minut eller två innan du ser en telemetri i utdata:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Använd följande kommando för att visa alla egenskaper som har skickats av enheten:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en IoT Plug and Play-enhet med hjälp av ett DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudie: skapa och testa en enhets kapacitets modell med Visual Studio Code](tutorial-pnp-visual-studio-code.md)
