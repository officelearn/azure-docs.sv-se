---
title: Snabbstart för att skicka telemetri till Azure IoT Hub (C) | Microsoft Docs
description: I den här snabbstarten kör du två C-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: 77b76ac5b30c4f5f647c532dbc5db68b396b3d20
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45636149"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs telemetrin från hubben med ett serverdelsprogram (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder ett C-exempelprogram från [SDK för Azure IoT-enheter så att C](iot-hub-device-sdk-c-intro.md) kan skicka telemetri till en IoT-hubb. SDK:n för Azure IoT-enheter är skrivna i [ANSI C (C99)](https://wikipedia.org/wiki/C99) för portabilitet och bred plattformskompatibilitet. Innan du kör exempelkoden skapar du en IoT-hubb och registrerar den simulerade enheten med den hubben.

Den här artikeln är skriven för Windows men du kan genomföra den här snabbstarten även på Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera [Visual Studio 2017](https://www.visualstudio.com/vs/) med arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

För den här snabbstarten kommer du att använda [SDK för Azure IoT-enheter](iot-hub-device-sdk-c-intro.md). 

Du kan använda SDK:n genom att installera paketen och biblioteken för följande miljöer:

* **Linux**: apt-get-paket är tillgängliga för Ubuntu 16.04 och 18.04 med hjälp av följande CPU-arkitekturer: amd64, arm64, armhf och i386. Mer information finns i [Använda apt-get för att skapa ett klientprojekt för C-enheten i Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: För utvecklare som skapar enhetsprogram på mbed-plattformen har vi publicerat ett bibliotek och exempel som gör att du kommer igång på bara några minuter med Azure IoT Hub. Mer information finns i [Använda mbed-biblioteket](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Om du utvecklar på Arduino, kan du utnyttja Azure IoT-biblioteket i Arduino IDE-bibliotekshanteraren. Mer information finns i [Azure IoT Hub-biblioteket för Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: SDK:n för IoT Hub-enheter är tillgänglig som CocoaPods för Mac- och iOS-enhetsutveckling. Mer information finns i [iOS-exempel för Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

I den här snabbstarten kommer du dock att förbereda en utvecklingsmiljö som ska användas för att klona och skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:n på GitHub inkluderar den exempelkod som används i den här snabbstarten. 


1. Ladda ned version 3.11.4 av [CMake-buildsystemet](https://cmake.org/download/). Kontrollera den hämta binära filen med hjälp av det motsvarande kryptografiska hashvärdet. I följande exempel används Windows PowerShell för att verifiera den kryptografisk hashen för version 3.11.4 av x64 MSI-distributionen:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Följande hash-värden för version 3.11.4 visades på CMake-webbplatsen när detta skrevs:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Storleken på den här lagringsplatsen är för närvarande cirka 220 MB. Den här åtgärden kan förväntas ta flera minuter att slutföra.


3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommando som skapar en version av SDK:n som är specifik för din utvecklingsklientsplattform. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake ..
    ```
    
    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet kommer du att använda Azure CLI med den [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att registrera en simulerad enhet.

1. Lägg till CLI-tillägget för IoT Hub och skapa enhetens identitet. Ersätt `{YourIoTHubName}` med det namn du angav för din IoT-hubb:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

    Om du väljer ett annat namn för din enhet måste du uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

2. Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten.


## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar en sträng som simulerad telemetri.

1. Använd en textredigerare och öppna källfilen iothub_convenience_sample.c och granska exempelkoden för att skicka telemetri. Filen finns på följande plats:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Hitta deklarationen för konstanten `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Ersätt värdet för konstanten `connectionString` med enhetens anslutningssträng som du antecknade tidigare. Spara dina ändringar i **iothub_convenience_sample.c**.

3. I ett terminalfönster navigerar du till projektkatalogen *iothub_convenience_sample* i CMake-katalogen som du skapade i Azure IoT C SDK.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Kör CMake med följande kommandorad för att skapa exemplet med det uppdaterade värdet för `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Kör följande kommando vid en kommandotolk för att köra det simulerade enhetsprogrammet:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till IoT-hubben:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb


I det här avsnittet kommer du att använda Azure CLI med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de enhetsmeddelanden som skickas av den simulerade enheten.

1. Med Azure CLI kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett C-program och läst telemetrin från hubben med hjälp av Azure CLI.

Om du vill lära dig mer om hur du utvecklar med Azure IoT Hub C SDK kan du fortsätta till följande instruktioner:

> [!div class="nextstepaction"]
> [Utveckla med hjälp av Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)