---
title: C-snabbstart för Azure IoT Hub-enhetsströmmar (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett C-program på tjänstsidan som kommunicerar med en IoT-enhet via en enhetsström.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830740"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i C via IoT Hub-enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Under den allmänna förhandsversionen stöder C SDK endast enhetsströmmar på enhetssidan. Därför omfattar den här snabbstarten bara instruktioner för att köra programmet på enhetssidan. Du bör köra ett tillhörande program på tjänstsidan som finns tillgängligt i guiderna för [C#-snabbstart](./quickstart-device-streams-echo-csharp.md) eller [Node.js-snabbstart](./quickstart-device-streams-echo-nodejs.md).

C-programmet på enhetssidan i den här snabbstarten har följande funktioner:

* Upprätta en enhetsström till en IoT-enhet.

* Ta emot data som skickas från tjänstsidan och returnera dem igen.

Koden visar initieringsprocessen för en enhetsström samt hur den används för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera [Visual Studio 2017](https://www.visualstudio.com/vs/) med arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

För den här snabbstarten kommer du att använda [SDK för Azure IoT-enheter](iot-hub-device-sdk-c-intro.md). Du kommer att förbereda en utvecklingsmiljö som ska användas för att klona och skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:n på GitHub inkluderar den exempelkod som används i den här snabbstarten. 


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

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Storleken på den här lagringsplatsen är för närvarande cirka 220 MB.

3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommando som skapar en version av SDK:n som är specifik för din utvecklingsklientsplattform. I Windows genereras en Visual Studio-lösning för den simulerade enheten i `cmake`-katalogen. 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

I Windows kör du följande kommandon i kommandotolken för utvecklare för din Visual Studio 2015- eller 2017-kommandotolk:

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet kommer du att använda Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyDevice såsom det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.


## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

För att kunna köra programmet på enhetssidan behöver du utföra följande steg:
- Konfigurera din utvecklingsmiljö med hjälp av anvisningarna i den här [artikeln om enhetsströmmar](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Ange autentiseringsuppgifter för enheten genom att redigera källfilen `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` och ange enhetens anslutningssträng.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- Kompilera koden på följande sätt:

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- Kör det kompilerade programmet:

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Som tidigare nämnts stöder IoT Hub C SDK endast enhetsströmmar på enhetssidan. För programmet på tjänstsidan använder du tillhörande tjänstprogram som finns tillgängliga i guiderna för [C#-snabbstart](./quickstart-device-streams-echo-csharp.md) eller [Node.js-snabbstart](./quickstart-device-streams-echo-nodejs.md).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett C-program och läst telemetrin från hubben med hjälp av Azure Cloud Shell.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)