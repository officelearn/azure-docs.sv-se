---
title: Skapa en IoT Plug and Play förhands gransknings enhet (Windows) | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden och se till att enheten är ansluten till IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4ee9bf218765ea4c3966e7f0a8b20a8108de7655
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931913"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Snabb start: Använd en enhets kapacitets modell för att skapa en IoT Plug and Play förhands gransknings enhet (Windows)

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en IoT plug and Play-enhet. Ett DCM är ofta kopplat till en produkt-SKU. Funktionerna som definieras i DCM är indelade i återanvändbara gränssnitt. Du kan generera Skeleton-enhets kod från ett DCM-kort. Den här snabb starten visar hur du använder VS Code i Windows för att skapa en IoT Plug and Play-enhet med hjälp av ett DCM.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten måste du installera följande program vara på den lokala datorn:

* [Bygg verktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med  **C++ build-verktyg** och **NuGet Package Manager-komponentens** arbets belastningar. Eller om du redan har [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbets belastningar installerade.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installera Azure IoT-verktyg

Använd följande steg för att installera [Azure IoT-verktyg för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter **Azure IoT-verktyg**.
1. Välj **Installera**.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databas](https://github.com/Azure/azure-iot-explorer/releases) sida genom att välja. msi-filen under "till gångar" för den senaste uppdateringen.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutnings strängen för din företags modell databas

Du hittar _anslutnings strängen för din företags modell databas_ i [Azure-certifierad för IoT Portal](https://preview.catalog.azureiotsolutions.com) -portalen när du loggar in med ett arbets-eller skol konto i Microsoft, eller ditt Microsoft partner-ID om du har ett. När du har loggat in väljer du **företags databas** och sedan **anslutnings strängar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du inte har en IoT-hubb följer du [de här anvisningarna för att skapa en](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDevice** med dina faktiska namn.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDevice>
```

Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

### <a name="get-azure-iot-device-sdk-for-c"></a>Hämta Azure IoT-enhetens SDK för C

I den här snabb starten förbereder du en utvecklings miljö genom att installera Azure IoT C-enhets-SDK via [Vcpkg](https://github.com/microsoft/vcpkg).

1. Öppna en kommandotolk. Kör följande kommando för att installera Vcpkg:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Om du vill koppla samman den globala [integrationen](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)kör du följande (Obs! kräver administratör vid första användningen):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Installera Azure IoT C-Vcpkg för enhets-SDK:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Redigera din modell

I den här snabb starten använder du en befintlig funktions modell för exempel enheter och tillhör ande gränssnitt.

1. Skapa en `pnp_app` katalog på din lokala enhet. Du använder den här mappen för enhets modellens filer och enhets koden stub.

1. Hämta [exempel](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) på [enhets kapacitets modell och gränssnitts](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) exempel och spara filer i `pnp_app` mapp.

    > [!TIP]
    > Om du vill ladda ned en fil från GitHub navigerar du till filen, högerklickar på **RAW**och väljer sedan **Spara länk som**.

1. Öppna `pnp_app` mapp med VS Code. Du kan visa filerna med IntelliSense:

    ![Enhets kapacitets modell](media/quickstart-create-pnp-device/dcm.png)

1. I de filer som du laddade ned ersätter du `<YOUR_COMPANY_NAME_HERE>` i fälten `@id` och `schema` med ett unikt värde. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu när du har ett DCM och tillhör ande gränssnitt kan du generera den enhets kod som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. Med mappen `pnp_app` öppen i VS Code, använder du **Ctrl + Shift + P** för att öppna kommando paletten, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT-Plug and Play CodeGen CLI tar det några sekunder att ladda ned och installera automatiskt.

1. Välj den **SampleDevice. capabilitymodel. JSON** -fil som ska användas för att generera enhets kod stub.

1. Ange projekt namnet **sample_device**. Detta är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **Via IoT Hub enhets anslutnings sträng** som anslutnings metod.

1. Välj **cmake-projekt i Windows** som projekt mal len.

1. Välj **via Vcpkg** som ett sätt att inkludera enhets-SDK: n.

1. En ny mapp med namnet **sample_device** skapas på samma plats som DCM-filen och i den genereras stub-filer för enhets koder. VS Code öppnar ett nytt fönster för att visa dessa.
    ![enhets kod](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Skapa och kör koden

Du kan använda enhets-SDK-källkoden för att skapa den genererade enhets koden stub. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en `cmake` under katalog i mappen `sample_device` och navigera till mappen:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa den genererade koden stub (Ersätt plats hållaren med katalogen för din Vcpkg-lagrings platsen):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Om du använder Visual Studio 2017 eller 2015 måste du ange CMake-generatorn baserat på de build-verktyg som du använder:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Om cmake inte kan hitta C++ din kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

1. När skapandet har slutförts kör du programmet och skickar IoT Hub-enhetens anslutnings sträng som en parameter.

    ```cmd\sh
    .\Debug\sample_device.exe "<device connection string>"
    ```

1. Enhets programmet börjar skicka data till IoT Hub.

    ![Enhets program som körs](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhets modell filer till modell databasen

Om du vill validera enhets koden med **Azure IoT Explorer**måste du publicera filerna på modell lagrings platsen.

1. Med mappen `pnp_app` öppen i VS Code, använder du **Ctrl + Shift + P** för att öppna kommando-paletten, skriver och väljer **IoT plugg & Play: skicka filer till modell databasen**.

1. Välj `SampleDevice.capabilitymodel.json` och `EnvironmentalSensor.interface.json` filer.

1. Ange din anslutnings sträng för företags modellens databas.

    > [!NOTE]
    > Anslutnings strängen krävs bara första gången du ansluter till lagrings platsen.

1. I VS Code output-fönster och meddelande kan du kontrol lera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhets modellens filer kan du prova att använda kommandot **IoT plug and Play: Logga ut modell databasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

1. Öppna Azure IoT Explorer. Du ser sidan **app-konfigurationer** .

1. Ange din _IoT Hub anslutnings sträng_ och välj **Anslut**.

1. När du har anslutit visas sidan enhets översikt.

1. Om du vill lägga till företags databasen väljer du **Inställningar**och sedan **+ Lägg till modulens definitions källa**, sedan **företags databasen**. Lägg till din anslutnings sträng för företags modell databas och välj **Spara och Anslut**.

1. På sidan enhets översikt letar du reda på enhets identiteten som du skapade tidigare och väljer den för att visa mer information.

1. Expandera gränssnittet med ID **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** om du vill se IoT plug and Play primitiver – egenskaper, kommandon och telemetri. Gränssnitts namnet som visas är det namn som du anger i när du skapade din modell.

1. Välj sidan **telemetri** och tryck på _börja_ för att visa de telemetridata som enheten skickar.

1. Välj sidan **egenskaper (inte skrivbar)** om du vill visa de icke skrivbara egenskaper som rapporteras av enheten.

1. Välj sidan **egenskaper (skrivbar)** om du vill visa de skrivbara egenskaperna som du kan uppdatera.

1. Expandera egenskaps **namn**, uppdatera med ett nytt namn och välj **Uppdatera skrivbar egenskap**.

1. Om du vill se det nya namnet som visas i kolumnen **rapporterad egenskap** väljer du knappen **Uppdatera** överst på sidan.

1. Välj sidan **kommandon** om du vill visa alla kommandon som enheten stöder.

1. Expandera kommandot **blinkar** och ange ett nytt intervall för blinknings tid. Välj **kommandot Skicka** för att anropa kommandot på enheten.

1. Gå till kommando tolken simulerad enhet och Läs igenom de utskrivna bekräftelse meddelandena för att kontrol lera att kommandona har körts som förväntat.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en IoT Plug and Play-enhet med hjälp av ett DCM.

Om du vill veta mer om DCMs och hur du skapar egna modeller fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Självstudie: skapa och testa en enhets kapacitets modell med Visual Studio Code](tutorial-pnp-visual-studio-code.md)
