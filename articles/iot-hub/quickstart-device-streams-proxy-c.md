---
title: Azure IoT Hub-enheten strömmar C-snabbstart för SSH och RDP
description: I den här snabbstarten kör du ett exempel C-program som fungerar som en proxy för att aktivera SSH- och RDP-scenarier över IoT Hub-enhetsströmmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 91cf594e74d91b8b4df2108d23459016fa843700
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586605"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP via en IoT Hub-enhetsström med hjälp av ett C-proxyprogram (förhandsgranskning)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. En översikt över installationen finns [på sidan Exempel på lokal proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Den här snabbstarten beskriver inställningarna för tunneltrafik av SSH-trafik (Secure Shell) (med port 22) via enhetsströmmar. Konfigurationen för RDP-trafik (Remote Desktop Protocol) är liknande och kräver en enkel konfigurationsändring. Eftersom enhetsströmmar är program- och protokolloberoende kan du ändra den här snabbstarten så att den passar andra typer av programtrafik.

## <a name="how-it-works"></a>Hur det fungerar

Följande bild visar hur enhets- och servicelokala proxyprogram möjliggör heltäckande anslutning mellan SSH-klienten och SSH-demonprocesserna. Under offentlig förhandsversion stöder C SDK endast enhetsströmmar på enhetssidan. Därför täcker den här snabbstarten instruktioner om att endast köra det enhetslokala proxyprogrammet. Om du vill skapa och köra det medföljande tjänstprogrammet följer du instruktionerna i någon av följande snabbstarter:

* [SSH/RDP över IoT Hub-enhetsströmmar med C#-proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP över IoT Hub-enhetsströmmar med NodeJS-proxy](./quickstart-device-streams-proxy-nodejs.md).

![Lokal proxyinställning](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Den servicelokala proxyn ansluter till IoT-hubben och startar en enhetsström till målenheten.

2. Den enhetslokala proxyn slutför ströminitieringshandskakningen och upprättar en direktuppspelningstunnel från slutpunkt till slutpunkt för strömning via IoT-hubbens slutpunkt för direktuppspelning till tjänstsidan.

3. Den enhetslokala proxyn ansluter till SSH-demonen som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhetslokala proxyprogrammet".

4. Den servicelokala proxyn väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i detta fall är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhetslokala proxyprogrammet". När användaren ansluter via SSH-klienten gör tunneln det möjligt att överföra SSH-programtrafik mellan SSH-klienten och serverprogrammen.

> [!NOTE]
> SSH-trafik som skickas via en enhetsström är tunnel via IoT-hubbens slutpunkt för direktuppspelning i stället för att skickas direkt mellan tjänst och enhet. Mer information finns i [fördelarna med att använda Iot Hub-enhetsströmmar](iot-hub-device-streams-overview.md#benefits). Dessutom illustrerar figuren SSH-demonen som körs på samma enhet (eller dator) som den enhetslokala proxyn. I denna snabbstart, förutsatt att SSH daemon IP-adress gör att enheten-lokal proxy och demonen att köras på olika maskiner också.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Förhandsgranskningen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:

  * USA, centrala
  * Centrala USA EUAP
  * Europa, norra
  * Sydostasien

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med [skrivbordsutvecklingen med C++-arbetsbelastning](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) aktiverad.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS)-specifika kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

För den här snabbstarten använder du [Azure IoT-enheten SDK för C](iot-hub-device-sdk-c-intro.md). Du förbereder en utvecklingsmiljö som används för att klona och skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK på GitHub innehåller exempelkoden som används i den här snabbstarten.

1. Ladda ner [CMake bygga systemet](https://cmake.org/download/).

    Det är viktigt att Visual Studio-förutsättningarna (Visual Studio och *skrivbordsutvecklingen med C++-arbetsbelastning)* installeras på datorn *innan* du startar CMake-installationen. När förutsättningarna är på plats och nedladdningen har verifierats kan du installera CMake-byggsystemet.

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

   * I Windows kör du följande kommandon i Kommandotolken för Utvecklare för Visual Studio 2015 eller 2017. En Visual Studio-lösning för den simulerade enheten genereras i *cmake-katalogen.*

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

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att registrera en simulerad enhet.

1. Om du vill skapa enhetsidentiteten kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet du registrerar rekommenderar vi att du använder *MyDevice* som visas. Om du väljer ett annat namn för enheten använder du det namnet i den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill hämta *enhetsanslutningssträngen* för den enhet som du just registrerade kör du följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en end-to-end-ström till tunnel-SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

1. Redigera källfilen **iothub_client_c2d_streaming_proxy_sample.c** i mappen `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`och ange enhetens anslutningssträng, målenhet IP/värdnamn och SSH-port 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompilera provet:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Kör det kompilerade programmet på enheten:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Som diskuteras i avsnittet "Hur det fungerar" kräver upprättande av en end-to-end-ström till tunnel SSH-trafik en lokal proxy i varje ände (på både tjänsten och enhetens sidor). Under offentlig förhandsversion stöder IoT Hub C SDK endast enhetsströmmar på enhetssidan. Om du vill skapa och köra den servicelokala proxyn följer du instruktionerna i någon av följande snabbstarter:

   * [SSH/RDP över IoT Hub-enhetsströmmar med C#-proxyappar](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP över IoT Hub-enhetsströmmar med node.js-proxyappar](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Upprätta en SSH-session

När både enhets- och servicelokala proxyservrar har körts använder du SSH-klientprogrammet och ansluter till den servicelokala proxyn på port 2222 (i stället för SSH-demonen direkt).

```cmd/sh
ssh {username}@localhost -p 2222
```

Nu uppmanas du att ange dina autentiseringsuppgifter i inloggningsfönstret ssh.

Följande bild visar konsolens utgång på den enhetslokala proxyn, som `IP_address:22`ansluter till SSH-demonen vid:

![Enhetslokal proxyutdata](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Följande bild visar konsolutgången för SSH-klientprogrammet. SSH-klienten kommunicerar till SSH-demonen genom att ansluta till port 22, som den servicelokala proxyn lyssnar på:

![SSH-klientutflöde](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, distribuerade en enhet och ett servicelokalt proxyprogram för att upprätta en enhetsström via IoT Hub och använde proxyservrarna för att tunnelsädSV-trafik.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
