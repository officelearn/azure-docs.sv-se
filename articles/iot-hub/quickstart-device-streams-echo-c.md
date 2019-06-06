---
title: Kommunicera med en app för enheter i C via Azure IoT Hub device strömmar (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett C enhetssidan program som kommunicerar med en IoT-enhet via en dataström med enheten.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 6a2fe71a1086559d90adf5c464323f353be431aa
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733315"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i C via IoT Hub-enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Allmänt tillgängliga förhandsversionen stöder C SDK enheten dataströmmar på enheter-sidan. Den här snabbstarten beskrivs därför instruktionerna för att köra enhetssidan programmet. Om du vill köra ett tillhörande tjänstsidan program, se:
 
   * [Kommunicera med appar för enheter i C# via IoT Hub device strömmar](./quickstart-device-streams-echo-csharp.md)
   * [Kommunicera med appar för enheter i Node.js via IoT Hub device strömmar](./quickstart-device-streams-echo-nodejs.md)

C-programmet på enhetssidan i den här snabbstarten har följande funktioner:

* Upprätta en enhetsström till en IoT-enhet.
* Ta emot data som skickas från tjänstsidan programmet och echo dem igen.

Koden visar hur initiation du en enhet ström, samt hur du använder den för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Förhandsversionen av enheten strömmar stöds för närvarande endast för IoT-hubbar som har skapats i följande regioner:

  * Centrala USA
  * USA, centrala – EUAP

* Installera [Visual Studio 2017](https://www.visualstudio.com/vs/) med den [skrivbordsutveckling med C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbetsbelastning aktiverat.

* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS)-specifika kommandon för att Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Den här snabbstartsguiden ska du använda den [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Förbereder du en utvecklingsmiljö som används för att klona och skapa den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK på GitHub innehåller exempelkod som används i den här snabbstarten.

1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

    Innan du påbörjar installationen CMake, är det viktigt som Visual Studio-krav (Visual Studio och *skrivbordsutveckling med C++*  arbetsbelastning) är installerat på datorn. När kraven är uppfyllda och du har kontrollerat nedladdningen, kan du installera CMake build-system.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Den här åtgärden tar några minuter.

3. Skapa en *cmake* underkatalog i rotkatalogen på Git-lagringsplatsen, som det visas i följande kommando och sedan gå till mappen.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommandon från den *cmake* katalog för att skapa en version av SDK: N som är specifik för din utvecklingsplattform för klienten.

   * I Linux:

      ```bash
      cmake ..
      make -j
      ```

   * I Windows, kör du följande kommandon i Kommandotolken för utvecklare för Visual Studio 2015 eller 2017. En Visual Studio-lösning för den simulerade enheten kommer att skapas i den *cmake* directory.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

Du måste registrera en enhet med IoT-hubben innan den kan ansluta. I det här avsnittet ska du använda Azure Cloud Shell med den [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) att registrera en simulerad enhet.

1. Om du vill skapa enhetens identitet, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.
   > * Använd *MyDevice*, som visas. Det är det namn du angav för den registrerade enheten. Om du väljer ett annat namn för din enhet kan använda det namnet i den här artikeln och uppdatera namnet på enheten i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Att hämta den *enhetsanslutningssträngen* för den enhet som du just registrerade, kör du följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Observera enhetens anslutningssträng för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enheten strömmar

I det här avsnittet kör både enhetssidan programmet och tjänstsidan programmet och kommunicera mellan två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Om du vill köra programmet enhetssidan, gör du följande:

1. Ange dina autentiseringsuppgifter för enheten genom att redigera den *iothub_client_c2d_streaming_sample.c* -källfilen i den *iothub_client/samples/iothub_client_c2d_streaming_sample* mapp och sedan tillhandahålla enhetens anslutningssträng.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Kompilera koden på följande sätt:

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

3. Kör det kompilerade programmet:

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

Som tidigare nämnts är IoT Hub C SDK har stöd för enheten strömmar på enheten endast. Om du vill skapa och köra programmet på tjänstsidan, följer du anvisningarna i någon av följande snabbstarter:

* [Kommunicera med en app för enheter i C# via IoT Hub device strömmar](./quickstart-device-streams-echo-csharp.md)
* [Kommunicera med en app för enheter i Node.js via IoT Hub device strömmar](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du ställa in en IoT-hubb, registrerade en enhet, upprätta en enhet dataström mellan ett C-program på enheten och ett annat program på serversidan och används dataströmmen för att skicka data fram och tillbaka mellan program.

Mer information om enheten strömmar finns:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
