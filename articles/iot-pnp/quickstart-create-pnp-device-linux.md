---
title: Skapa en Azure IoT Plug and Play Preview-enhet (Linux) | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden och se till att enheten är ansluten till IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: ff8303b6af73605aae82bae4d70f9648154f9744
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406241"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snabb start: Använd en enhets kapacitets modell för att skapa en IoT Plug and Play Preview-enhet (Linux)

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en IoT plug and Play-enhet. Ett DCM är ofta kopplat till en produkt-SKU. Funktionerna som definieras i DCM är indelade i återanvändbara gränssnitt. Du kan generera Skeleton-enhets kod från ett DCM-kort. Den här snabb starten visar hur du använder VS Code på Ubuntu Linux för att skapa en IoT Plug and Play-enhet med hjälp av DCM.

## <a name="prerequisites"></a>Förutsättningar

I den här snabb starten förutsätter vi att du använder Ubuntu Linux med en Skriv bords miljö. Stegen i den här självstudien har testats med Ubuntu 18,04.

För att slutföra den här snabb starten måste du installera följande program vara på den lokala Linux-datorn:

* Installera **gcc**, **git**, **cmake**och alla beroenden med kommandot `apt-get`:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

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

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du inte redan har en IoT-hubb att använda följer du resten av det här avsnittet för att skapa ett.

Om du använder Azure CLI lokalt bör `az`-versionen vara **2.0.75** eller senare, Azure Cloud Shell använder den senaste versionen. Använd kommandot `az --version` för att kontrol lera vilken version som är installerad på datorn.

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stegen i den här snabb starten kräver version **0.8.5** eller senare av tillägget. Använd kommandot `az extension list` för att kontrol lera vilken version du har installerat och `az extension update` kommandot för att uppdatera vid behov.

Om du inte har en IoT-hubb skapar du en med följande kommandon och ersätter `<YourIoTHubName>` med ett unikt namn som du väljer. Om du kör de här kommandona lokalt loggar du först in på Azure-prenumerationen med hjälp av `az login`. Om du kör de här kommandona i Azure Cloud Shell är du inloggad automatiskt:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name <YourIoTHubName> \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Föregående kommandon skapar en resurs grupp med namnet `pnpquickstarts_rg` och en IoT-hubb i den centrala regionen USA.

> [!IMPORTANT]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Kör följande kommando för att skapa en enhets identitet i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDeviceID** med ditt eget _IoT Hub namn_ och ett valfritt _enhets-ID_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Kör följande kommandon för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade (Anmärkning för användning senare).

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

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

1. Skapa en `pnp_app` katalog på din lokala enhet. Du använder den här mappen för enhets modellens filer och enhets koden stub.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Ladda ned enhetens kapacitets modell och gränssnitts exempel filer till mappen `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Öppna `pnp_app` mapp med VS Code. Du kan visa filerna med IntelliSense:

    ![Enhets kapacitets modell](media/quickstart-create-pnp-device-linux/dcm.png)

1. I de filer som du laddade ned ersätter du `<YOUR_COMPANY_NAME_HERE>` i fälten `@id` och `schema` med ett unikt värde. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu när du har ett DCM och tillhör ande gränssnitt kan du generera den enhets kod som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. Med mappen `pnp_app` öppen i VS Code, använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator-verktyget tar det några sekunder att ladda ned och installera automatiskt.

1. Välj den **SampleDevice. capabilitymodel. JSON** -fil som ska användas för att generera enhets kod stub.

1. Ange projekt namnet **sample_device**. Detta är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **Via IoT Hub enhets anslutnings sträng** som anslutnings metod.

1. Välj **cmake-projekt på Linux** som projekt mal len.

1. Välj **via Vcpkg** som metod för att inkludera enhets-SDK: n.

1. En ny mapp med namnet **sample_device** skapas på samma plats som DCM-filen och i den genereras stub-filer för enhets koder. VS Code öppnar ett nytt fönster för att visa dessa.
    ![enhets kod](media/quickstart-create-pnp-device-linux/device-code.png)

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

1. Med mappen `pnp_app` öppen i VS Code, använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver och väljer **IoT plugg & Play: skicka filer till modell databasen**.

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
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDevice>
```

Använd följande kommando för att visa alla egenskaper som har skickats av enheten:

```azurecli-interactive
az iot dt list-properties --device-id <YourDevice> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en IoT Plug and Play-enhet med hjälp av ett DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudie: skapa och testa en enhets kapacitets modell med Visual Studio Code](tutorial-pnp-visual-studio-code.md)
