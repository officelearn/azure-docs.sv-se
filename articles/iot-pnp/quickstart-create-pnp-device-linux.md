---
title: Skapa en Azure IoT Plug and Play Preview-enhet (Linux) | Microsoft-dokument
description: Använd en enhetskapacitetsmodell för att generera enhetskod. Kör sedan enhetskoden och se enheten ansluta till din IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550544"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snabbstart: Använd en enhetskapacitetsmodell för att skapa en IoT Plug and Play Preview-enhet (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

En _enhetskapacitetsmodell_ (DCM) beskriver funktionerna hos en IoT Plug and Play-enhet. En DCM associeras ofta med en produkt SKU. De funktioner som definieras i DCM är organiserade i återanvändbara gränssnitt. Du kan generera skelettenhetskod från en DCM. Den här snabbstarten visar hur du använder VS-kod på Ubuntu Linux för att skapa en IoT Plug and Play-enhet med hjälp av en DCM.

## <a name="prerequisites"></a>Krav

Den här snabbstarten förutsätter att du använder Ubuntu Linux med en skrivbordsmiljö. Stegen i den här självstudien testades med Ubuntu 18.04.

För att slutföra den här snabbstarten måste du installera följande programvara på din lokala Linux-dator:

* Installera **GCC,** **Git**, **cmake**och `apt-get` alla beroenden med kommandot:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Kontrollera att `cmake` versionen av är över **2.8.12** och versionen av **GCC** är över **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Gör så här för att installera [azure IoT Tools for VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Code-tilläggspaketet:

1. Välj fliken **Tillägg** i VS-kod.
1. Sök efter **Azure IoT Tools**.
1. Välj **Installera**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutningssträngen för företagets modelldatabas

Du hittar _anslutningssträngen för företagsmodelllagringsplats_ i [Portalen för Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) när du loggar in med ett Microsoft-arbets- eller skolkonto eller ditt Microsoft Partner-ID om du har ett. När du har loggat in väljer du **Företagsdatabas** och sedan **Anslutningssträngar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten använder du [Vcpkg-bibliotekshanteraren](https://github.com/microsoft/vcpkg) för att installera Azure IoT C-enheten SDK i utvecklingsmiljön.

Öppna ett gränssnitt. Kör följande kommando för att installera Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="author-your-model"></a>Skapa din modell

I den här snabbstarten använder du en befintlig exempelenhetsfunktionsmodell och tillhörande gränssnitt.

1. Skapa `pnp_app` en katalog i den lokala enheten. Du använder den här mappen för enhetsmodellfiler och enhetskod stub.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Hämta enhetskapacitetsmodellen och gränssnittsprovfilerna `pnp_app` till mappen.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Öppna `pnp_app` mappen med VS-kod. Du kan visa filerna med IntelliSense:

    ![Modell för enhetskapacitet](media/quickstart-create-pnp-device-linux/dcm.png)

1. Ersätt `<YOUR_COMPANY_NAME_HERE>` i fälten `@id` och `schema` med ett unikt värde i filerna som du hämtade. Använd bara tecknen a-z, A-Ö, 0-9 och understreck. Mer information finns i [Digital Twin-identifierarformat](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-kod stub

Nu när du har en DCM och dess associerade gränssnitt kan du generera enhetskoden som implementerar modellen. Så här genererar du C-kodssubsen i VS-kod:

1. När `pnp_app` mappen är öppen i VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, anger **IoT Plug and Play**och väljer **Generera Enhetskodsstub.**

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator verktyget, det tar några sekunder att ladda ner och installera automatiskt.

1. Välj filen **SampleDevice.capabilitymodel.json** som ska användas för att generera enhetskoden stub.

1. Ange projektnamnet **sample_device**. Detta kommer att vara namnet på ditt enhetsprogram.

1. Välj **ANSI C** som språk.

1. Välj **Anslutningssträngen för Via IoT Hub-enheten** som anslutningsmetod.

1. Välj **CMake Project på Linux** som projektmall.

1. Välj **Via Vcpkg** som sätt att inkludera enheten SDK.

1. En ny mapp som heter **sample_device** skapas på samma plats som DCM-filen, och i den finns de genererade enhetskodssubenfilerna. VS-kod öppnar ett nytt fönster för att visa dessa.
    ![Enhetskod](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du använder enhetenSDK-källkoden för att skapa den genererade enhetskodsstuben. Programmet du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en **CMake-byggmapp** för **sample_device-programmet:**

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Kör CMake för att skapa din app med SDK. Följande kommando förutsätter att du har installerat **vcpkg** i hemmappen:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. När versionen har slutförts kör du programmet som skickar anslutningssträngen för IoT-hubbenhet som parameter.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Enhetsprogrammet börjar skicka data till IoT Hub.

    ![Enhetsapp körs](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhetsmodellfiler till modelldatabasen

Om du vill validera enhetskoden med **az** CLI måste du publicera filerna till modelldatabasen.

1. När `pnp_app` mappen är öppen i VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, skriver och väljer **IoT Plug & Play: Skicka filer till Modelldatabasen**.

1. Markera `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` och filer.

1. Ange anslutningssträngen för företagsmodelldatabasen.

    > [!NOTE]
    > Anslutningssträngen krävs bara första gången du ansluter till databasen.

1. I utdatafönstret vs-kod och meddelande kan du kontrollera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhetsmodellfilerna kan du prova att använda **kommando-IoT Plug and Play: Logga ut Modelldatabasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Använda Azure IoT CLI för att validera koden

När enhetsklientexemplet har startat kan du kontrollera att det fungerar med Azure CLI.

Använd följande kommando för att visa den telemetri som exempelenheten skickar. Du kan behöva vänta en minut eller två innan du ser någon telemetri i utdata:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Använd följande kommando för att visa alla egenskaper som skickas av enheten:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en IoT Plug and Play-enhet med hjälp av en DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa och testa en enhetskapacitetsmodell med Visual Studio-kod](tutorial-pnp-visual-studio-code.md)
