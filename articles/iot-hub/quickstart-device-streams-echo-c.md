---
title: Kommunicera till enhetsapp i C med Azure IoT Hub-enhetsströmmar
description: I den här snabbstarten kör du ett C-program på enhetssidan som kommunicerar med en IoT-enhet via en enhetsström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675534"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i C via IoT Hub-enhetsströmmar (förhandsgranskning)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Under offentlig förhandsversion stöder C SDK endast enhetsströmmar på enhetssidan. Därför täcker den här snabbstarten instruktioner om att endast köra programmet på enhetssidan. Om du vill köra ett motsvarande program på tjänstsidan läser du följande artiklar:

* [Kommunicera med enhetsappar i C# via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-csharp.md)

* [Kommunicera med enhetsappar i Node.js via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-nodejs.md)

C-programmet på enhetssidan i den här snabbstarten har följande funktioner:

* Upprätta en enhetsström till en IoT-enhet.

* Ta emot data som skickas från tjänstprogrammet och eko tillbaka dem.

Koden visar initieringsprocessen för en enhetsström, samt hur du använder den för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande förutsättningar:

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med **skrivbordsutvecklingen med C++-arbetsbelastning** aktiverad.

* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS)-specifika kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Förhandsgranskningen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:

  * USA, centrala
  * Centrala USA EUAP
  * Europa, norra
  * Sydostasien

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

För den här snabbstarten använder du [Azure IoT-enheten SDK för C](iot-hub-device-sdk-c-intro.md). Du förbereder en utvecklingsmiljö som används för att klona och skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK på GitHub innehåller exempelkoden som används i den här snabbstarten.

   > [!NOTE]
   > Innan du påbörjar den här proceduren bör du se till att Visual Studio installeras med **skrivbordsutvecklingen med C++-arbetsbelastningen.**

1. Installera [CMake-byggsystemet](https://cmake.org/download/) enligt beskrivningen på nedladdningssidan.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden bör ta några minuter.

1. Skapa en *cmake-underkatalog* i rotkatalogen i git-databasen och navigera till den mappen. Kör följande kommandon från katalogen *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon från *cmake-katalogen* för att skapa en version av SDK som är specifik för din utvecklingsklientplattform.

   * I Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Öppna en [kommandotolk för Visual Studio i Windows](/dotnet/framework/tools/developer-command-prompt-for-vs). Kör kommandot för din version av Visual Studio. Den här snabbstarten använder Visual Studio 2019. Dessa kommandon skapar en Visual Studio-lösning för den simulerade enheten i *cmake-katalogen.*

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Du måste registrera en enhet med din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att registrera en simulerad enhet.

1. Om du vill skapa enhetsidentiteten kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet du registrerar rekommenderar vi att du använder *MyDevice* som visas. Om du väljer ett annat namn för enheten använder du det namnet i den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill hämta *enhetsanslutningssträngen* för den enhet som du just registrerade kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enhetsströmmar

I det här avsnittet kör du både enhetsprogrammet och tjänstprogrammet och kommunicerar mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Så här kör du programmet på enhetssidan:

1. Ange enhetsautentiseringsuppgifter genom att redigera **källfilen iothub_client_c2d_streaming_sample.c** i `iothub_client/samples/iothub_client_c2d_streaming_sample` mappen och lägga till enhetens anslutningssträng.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Kompilera koden med följande kommandon:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Kör det kompilerade programmet:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Som tidigare nämnts stöder IoT Hub C SDK endast enhetsströmmar på enhetssidan. Om du vill skapa och köra det medföljande tjänstprogrammet följer du instruktionerna i någon av följande snabbstarter:

* [Kommunicera med en enhetsapp i C# via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-csharp.md)

* [Kommunicera med en enhetsapp i Node.js via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, upprättade en enhetsström mellan ett C-program på enheten och ett annat program på tjänstsidan och använde strömmen för att skicka data fram och tillbaka mellan programmen.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
