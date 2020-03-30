---
title: Skapa en IoT Plug and Play Preview-enhet (Windows) | Microsoft-dokument
description: Använd en enhetskapacitetsmodell för att generera enhetskod. Kör sedan enhetskoden och se enheten ansluta till din IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75867494"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Snabbstart: Använd en enhetskapacitetsmodell för att skapa en IoT Plug and Play Preview-enhet (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

En _enhetskapacitetsmodell_ (DCM) beskriver funktionerna hos en IoT Plug and Play-enhet. En DCM associeras ofta med en produkt SKU. De funktioner som definieras i DCM är organiserade i återanvändbara gränssnitt. Du kan generera skelettenhetskod från en DCM. Den här snabbstarten visar hur du använder VS-kod i Windows för att skapa en IoT Plug and Play-enhet med hjälp av en DCM.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du installera följande programvara på din lokala dator:

* [Byggverktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med **C++ byggverktyg** och **NuGet-komponentarbetsbelastningar för pakethanteraren.** Eller om du redan har [Visual Studio (Community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbetsbelastningar installerade.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Gör så här för att installera [azure IoT Tools for VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Code-tilläggspaketet:

1. Välj fliken **Tillägg** i VS-kod.
1. Sök efter **Azure IoT Tools**.
1. Välj **Installera**.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT-utforskaren

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databassida](https://github.com/Azure/azure-iot-explorer/releases) genom att välja MSI-filen under "Tillgångar" för den senaste uppdateringen.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutningssträngen för företagets modelldatabas

Du hittar _anslutningssträngen för företagsmodelllagringsplats_ i [Portalen för Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) när du loggar in med ett Microsoft-arbets- eller skolkonto eller ditt Microsoft Partner-ID om du har ett. När du har loggat in väljer du **Företagsdatabas** och sedan **Anslutningssträngar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten använder du [Vcpkg-bibliotekshanteraren](https://github.com/microsoft/vcpkg) för att installera Azure IoT C-enheten SDK i utvecklingsmiljön.

1. Öppna en kommandotolk. Kör följande kommando för att installera Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Sedan, för att ansluta användaromfattande [integration](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md), kör följande (obs: kräver admin vid första användningen):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installera Azure IoT C-enhet SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Skapa din modell

I den här snabbstarten använder du en befintlig exempelenhetsfunktionsmodell och tillhörande gränssnitt.

1. Skapa `pnp_app` en mapp på den lokala enheten. Du använder den här mappen för enhetsmodellfiler och enhetskod stub.

1. Hämta [enhetskapacitetsmodellen och gränssnittsprovfilerna](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) och [gränssnittsprovet](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) och spara filerna i `pnp_app` mappen.

    > [!TIP]
    > Om du vill hämta en fil från GitHub navigerar du till filen, högerklickar på **Raw**och väljer sedan **Spara länk som**.

1. Öppna `pnp_app` mappen med VS-kod. Du kan visa filerna med IntelliSense:

    ![Modell för enhetskapacitet](media/quickstart-create-pnp-device/dcm.png)

1. Ersätt `<YOUR_COMPANY_NAME_HERE>` i fälten `@id` och `schema` med ett unikt värde i filerna som du hämtade. Använd bara tecknen a-z, A-Ö, 0-9 och understreck. Mer information finns i [Digital Twin-identifierarformat](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-kod stub

Nu när du har en DCM och dess associerade gränssnitt kan du generera enhetskoden som implementerar modellen. Så här genererar du C-kodssubsen i VS-kod:

1. När `pnp_app` mappen är öppen i VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, anger **IoT Plug and Play**och väljer **Generera Enhetskodsstub.**

    > [!NOTE]
    > Första gången du använder IoT Plug and Play CodeGen CLI tar det några sekunder att hämta och installera automatiskt.

1. Välj filen **SampleDevice.capabilitymodel.json** som ska användas för att generera enhetskoden stub.

1. Ange projektnamnet **sample_device**. Det här är namnet på enhetsprogrammet.

1. Välj **ANSI C** som språk.

1. Välj **Anslutningssträngen för Via IoT Hub-enheten** som anslutningsmetod.

1. Välj **CMake Project i Windows** som projektmall.

1. Välj **Via Vcpkg** som sätt att inkludera enheten SDK.

1. En ny mapp som heter **sample_device** skapas på samma plats som DCM-filen, och i den finns de genererade enhetskodssubenfilerna. VS-kod öppnar ett nytt fönster för att visa dessa.
    ![Enhetskod](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du använder Vcpkg-paketet för att skapa den genererade enhetskoden stub. Programmet du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa `cmake` en underkatalog `sample_device` i mappen och navigera till den mappen:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade kodsuben (ersätter platshållaren med katalogen för din Vcpkg-repo):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de byggverktyg du använder:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Om cmake inte kan hitta din C++-kompilator får du byggfel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. När versionen har slutförts kör du programmet och skickar anslutningssträngen för IoT-hubbenhet som en parameter.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Enhetsprogrammet börjar skicka data till IoT Hub.

    ![Enhetsapp körs](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhetsmodellfiler till modelldatabasen

Om du vill validera enhetskoden med **Azure IoT Explorer**måste du publicera filerna till modelldatabasen.

1. När `pnp_app` mappen är öppen i VS-kod använder du **Ctrl+Skift+P** för att öppna kommandopaletten, skriver och väljer **IoT Plug & Play: Skicka filer till Modelldatabasen**.

1. Markera `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` och filer.

1. Ange anslutningssträngen för företagsmodelldatabasen.

    > [!NOTE]
    > Anslutningssträngen krävs bara första gången du ansluter till databasen.

1. I utdatafönstret vs-kod och meddelande kan du kontrollera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhetsmodellfilerna kan du prova att använda **kommando-IoT Plug and Play: Logga ut Modelldatabasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använda Azure IoT explorer för att validera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill lägga till företagsdatabasen väljer du **Inställningar**och sedan **+ Lägg till moduldefinitionskälla**och sedan **Företagsdatabas**. Lägg till anslutningssträngen för företagsmodelldatabasen och välj **Spara och anslut**.

1. Leta reda på den enhetsidentitet som du skapade tidigare på sidan **Enhetsöversikt.** När enhetsprogrammet fortfarande körs i kommandotolken kontrollerar du att enhetens **anslutningstillstånd** i Azure IoT Explorer rapporterar som _Ansluten_ (om inte, tryck **på Uppdatera** tills den är). Välj den enhet som ska visas för mer information.

1. Expandera gränssnittet med ID **urn:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** för att se IoT Plug and Play-primitiverna - egenskaper, kommandon och telemetri. Gränssnittsnamnet som visas är det namn du sätter in när du redigerar modellen.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en IoT Plug and Play-enhet med hjälp av en DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa och testa en enhetskapacitetsmodell med Visual Studio-kod](tutorial-pnp-visual-studio-code.md)
