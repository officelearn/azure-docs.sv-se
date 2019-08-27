---
title: Skapa en IoT Plug and Play förhands gransknings enhet | Microsoft Docs
description: Använd en enhets kapacitets modell för att generera enhets kod. Kör sedan enhets koden och se till att enheten är ansluten till IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 386c2fa23e8d01f696ef3cf6078bac5fcec58f05
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050128"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Snabbstart: Använd en modell för enhets kapacitet för att skapa en IoT Plug and Play-enhet

En _enhets kapacitets modell_ (DCM) beskriver funktionerna i en IoT plug and Play-enhet. Ett DCM är ofta kopplat till en produkt-SKU. Funktionerna som definieras i DCM är indelade i återanvändbara gränssnitt. Du kan generera Skeleton-enhets kod från ett DCM-kort. Den här snabb starten visar hur du använder VS Code för att skapa en IoT Plug and Play-enhet med hjälp av ett DCM.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du installera följande program vara på den lokala datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++**  arbets belastning när du installerar Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-device-workbench"></a>Installera Azure IoT Device Workbench

Använd följande steg för att installera tillägget Azure IoT Device Workbench i VS Code:

1. I VS Code väljer du fliken **tillägg** .
1. Sök efter **Azure IoT Device Workbench**.
1. Välj **Installera**.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera verktyget Azure IoT Explorer från den [senaste versions](https://github.com/Azure/azure-iot-explorer/releases) sidan.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Hämta anslutnings strängen för din företags modell databas

Du hittar _anslutnings strängen för din företags modell databas_ i [Azure-certifierad för IoT Portal](https://preview.catalog.azureiotsolutions.com) -portalen när du loggar in med ett arbets-eller skol konto i Microsoft, eller ditt Microsoft partner-ID om du har ett. När du har loggat in väljer du **företags databas** och sedan **anslutnings strängar**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Lägg till Microsoft Azure IoT-tillägg för Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDevice** med dina faktiska namn. Om du inte har något IoT Hub, följer du [dessa anvisningar för att skapa ett](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Kör följande kommandon för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Kör följande kommandon för att hämta _anslutnings strängen för IoT Hub_ för hubben:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

### <a name="get-azure-iot-device-sdk-for-c"></a>Hämta Azure IoT-enhetens SDK för C

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT C-enhetens SDK.

1. Öppna en kommandotolk. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en `pnp_app` under katalog i roten för den lokala klonen av lagrings platsen. Du använder den här mappen för enhets modellens filer och enhets koden stub.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Redigera din modell

I den här snabb starten använder du en befintlig funktions modell för exempel enheter och tillhör ande gränssnitt.

1. Hämta exemplet på [enhets kapacitets modellen](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) och [gränssnittet](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) och spara `pnp_app` filer i mappen.

    > [!TIP]
    > Om du vill ladda ned en fil från GitHub navigerar du till filen, högerklickar på **RAW**och väljer sedan **Spara länk som**.

1. Öppna `pnp_app` en mapp med vs Code. Du kan visa filerna med IntelliSense:

    ![Enhets kapacitets modell](media/quickstart-create-pnp-device/dcm.png)

1. Ersätt `<YOUR_COMPANY_NAME_HERE>` ifälten`schema`ochmed ett unikt värde i filerna som du laddade ned. `@id` Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generera C-koden stub

Nu har du ett DCM och tillhör ande gränssnitt, du kan generera enhets koden som implementerar modellen. Så här genererar du C-koden stub i VS Code:

1. När mappen med DCM-filer är öppen använder du **Ctrl + Shift + P** för att öppna paletten kommando, ange **IoT plug and Play**och väljer **generera enhets kod stub**.

    > [!NOTE]
    > Första gången du använder IoT Plug and Play Code Generator-verktyget tar det några sekunder att ladda ned.

1. Välj den DCM-fil som du vill använda för att generera enhets kodens stub.

1. Ange projekt namnet **sample_device**, det är namnet på enhets programmet.

1. Välj **ANSI C** som språk.

1. Välj **cmake-projekt** som projekt typ.

1. Välj **Via IoT Hub enhets anslutnings sträng** som anslutnings metod.

1. VS Code öppnar ett nytt fönster med genererad enhets kod stub-filer.
    ![Enhets kod](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att skapa den genererade enhets koden stub. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I vs Code öppnar `CMakeLists.txt` du mappen för enhets-SDK-rotmappen.

1. Lägg till raden nedan längst ned i `CMakeLists.txt` filen för att inkludera enhets koden stub-mapp när du kompilerar:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Skapa en cmake under katalog i rotmappen för enhetens SDK och navigera till mappen:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhets-SDK och den genererade koden stub:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Om cmake inte kan hitta C++ din kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

1. När skapandet har slutförts kör du ditt program och skickar IoT Hub-enhetens anslutnings sträng som parameter.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. Enhets programmet börjar skicka data till IoT Hub.

    ![Enhets program som körs](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Verifiera koden

### <a name="publish-device-model-files-to-model-repository"></a>Publicera enhets modell filer till modell databasen

Om du vill validera enhets koden med **Azure IoT Explorer**måste du publicera filerna på modell lagrings platsen.

1. När mappen med DCM-filer är öppen använder du **Ctrl + Shift + P** för att öppna kommando-paletten, **skriver och väljer IoT Plug-& Play: Skicka filer till modell lagrings platsen**.

1. Välj `SampleDevice.capabilitymodel.json` och`EnvironmentalSensor.interface.json` filer.

1. Ange din anslutnings sträng för företags modellens databas.

    > [!NOTE]
    > Anslutnings strängen krävs bara första gången du ansluter till lagrings platsen.

1. I VS Code output-fönster och meddelande kan du kontrol lera att filerna har publicerats.

    > [!NOTE]
    > Om du får fel när du publicerar enhets modellens filer kan du försöka använda **kommandot IoT plug and Play: Logga ut modell databasen** för att logga ut och gå igenom stegen igen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

1. Öppna Azure IoT Explorer, du ser sidan **AppData** .

1. Ange din IoT Hub anslutnings sträng och klicka på **Anslut**.

1. När du har anslutit visas sidan enhets översikt.

1. Om du vill lägga till företags databasen väljer du **Inställningar**, sedan **+ ny**och sedan **företagets lagrings plats**.

1. Lägg till din anslutnings sträng för företagets modell databas. Välj **Anslut**.

1. På sidan enhets översikt letar du reda på enhets identiteten som du skapade tidigare och väljer den för att visa mer information.

1. Expandera gränssnittet med ID **urn: azureiot: EnvironmentalSensor: 1** om du vill se IoT plug and Play primitiver – egenskaper, kommandon och telemetri.

1. Välj sidan **telemetri** för att visa de telemetridata som enheten skickar.

1. Välj sidan **egenskaper (inte skrivbar)** om du vill visa de icke skrivbara egenskaper som rapporteras av enheten.

1. Välj sidan **egenskaper (skrivbar)** om du vill visa de skrivbara egenskaperna som du kan uppdatera.

1. Expandera egenskaps **namn**, uppdatera med ett nytt namn och välj **Uppdatera skrivbar egenskap**. 
2. Om du vill se det nya namnet visas i kolumnen **rapporterad egenskap** klickar du på knappen **Uppdatera** överst på sidan.

1. Välj **kommando** sidan för att visa alla kommandon som enheten stöder.

1. Expandera kommandot **blinkar** och ange ett nytt intervall för blinknings tid. Välj **kommandot Skicka** för att anropa kommandot på enheten.

1. Gå till den simulerade enheten för att kontrol lera att kommandot körs som förväntat.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en IoT Plug and Play-enhet med hjälp av ett DCM.

Om du vill veta mer om IoT Plug and Play fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Skapa och testa en enhets kapacitets modell med Visual Studio Code](tutorial-pnp-visual-studio-code.md)
