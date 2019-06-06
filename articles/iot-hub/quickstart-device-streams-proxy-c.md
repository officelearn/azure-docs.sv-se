---
title: Azure IoT Hub device strömmar C Snabbstart för SSH och RDP (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett exempel på C-program som fungerar som proxy för att aktivera SSH och RDP-scenarier via IoT Hub device strömmar.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: e21d7381831553f8d82b00d9ed3be0b03f13bca9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735129"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP över en ström för IoT Hub-enhet med C-proxyprogrammet (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. En översikt över installationen finns i [exempelsida för lokal Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Den här snabbstarten beskriver inställningarna för tunneltrafik Secure Shell (SSH) trafik (använder port 22) via enheten strömmar. Installationsprogrammet för Remote Desktop Protocol (RDP)-trafik är liknande och kräver någon enkel konfigurationsändring. Eftersom enheten strömmar och protocol-programoberoende, kan du ändra den här snabbstarten för att hantera andra typer av trafik.

## <a name="how-it-works"></a>Hur det fungerar

Följande bild visar hur enheten och tjänsten lokal proxy-program kan slutpunkt till slutpunkt-anslutning mellan SSH-klient och SSH-daemon processer. Allmänt tillgängliga förhandsversionen stöder C SDK enheten dataströmmar på enheter-sidan. Den här snabbstarten beskrivs därför instruktionerna för att köra endast enheten-lokal proxy-program. Du bör köra något av följande på tjänstsidan snabbstarter:

* [SSH/RDP över IoT Hub device strömmar med C# proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP över IoT Hub device strömmar med NodeJS proxy](./quickstart-device-streams-proxy-nodejs.md).

![Installationen av lokal webbprogramproxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Service-lokal proxy ansluter till IoT hub och startar en enhet ström till målenheten.

2. Enhet-lokal-proxy har slutförts stream initiation handshake och upprättar en strömmande slutpunkt till slutpunkt-tunnel via IoT-hubbens slutpunkt för direktuppspelning till serversidan.

3. Proxy för lokal enhet ansluter till SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras, enligt beskrivningen i avsnittet ”kör enhet-lokal proxy-program”.

4. Service-lokal proxy väntar tills nya SSH-anslutningar från en användare genom att lyssna på en avsedda port, som i det här fallet är port 2222. Den här inställningen kan konfigureras, enligt beskrivningen i avsnittet ”kör enhet-lokal proxy-program”. När användaren ansluter via SSH-klient, kan tunneln SSH programtrafik överförs mellan klienten och servern program för SSH.

> [!NOTE]
> SSH-trafik som skickas över en enhet dataström är väg genom IoT-hubbens slutpunkten för direktuppspelning i stället skickas direkt mellan tjänster och enheter. Mer information finns i den [fördelarna med att använda Iot Hub device strömmar](iot-hub-device-streams-overview.md#benefits). Bilden visar dessutom SSH-daemon som körs på samma enhet (eller dator) som proxy för lokal enhet. I den här snabbstarten kan med SSH-daemon IP-adressen enheten-lokal proxy och daemon ska köras på olika datorer samt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Förhandsversionen av enheten strömmar stöds för närvarande endast för IoT-hubbar som har skapats i följande regioner:

  * Centrala USA
  * USA, centrala – EUAP

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med den [skrivbordsutveckling med C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbetsbelastning aktiverat.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS)-specifika kommandon för att Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Den här snabbstartsguiden ska du använda den [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Förbereder du en utvecklingsmiljö som används för att klona och skapa den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK på GitHub innehåller exempelkod som används i den här snabbstarten.

1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

    Det är viktigt som Visual Studio-krav (Visual Studio och *skrivbordsutveckling med C++*  arbetsbelastning) är installerat på datorn, *innan* du startar installationen av CMake. När kraven är uppfyllda och hämtningen har verifierats, kan du installera CMake build-system.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Du bör förvänta dig den här åtgärden kan ta några minuter.

1. Skapa en *cmake* underkatalog i rotkatalogen på Git-lagringsplatsen, som visas i följande kommando, och navigera till mappen.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon från den *cmake* katalog för att skapa en version av SDK: N som är specifik för din utvecklingsplattform för klienten.

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

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet ska du använda Azure Cloud Shell med den [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) att registrera en simulerad enhet.

1. Om du vill skapa enhetens identitet, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.
   > * Använd *MyDevice*, som visas. Det är det namn du angav för den registrerade enheten. Om du väljer ett annat namn för din enhet kan använda det namnet i den här artikeln och uppdatera namnet på enheten i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Att hämta den *enhetsanslutningssträngen* för den enhet som du just registrerade, kör du följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Observera enhetens anslutningssträng för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet ska upprätta du en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

1. Redigera källfilen *iothub_client_c2d_streaming_sample.c* i mappen *iothub_client/samples/iothub_client_c2d_streaming_sample*, och ange enhetens anslutningssträng, mål enheten IP/värdnamn och SSH-porten 22:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Kompilera exemplet:

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

Som beskrivs i avsnittet ”Så här fungerar det” kan kräver upprätta en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik en lokal proxy i slutet (för både tjänsten och de enhet). Allmänt tillgängliga förhandsversionen stöder IoT Hub C SDK enheten dataströmmar på enheter-sidan. Om du vill skapa och köra service-lokal proxy, följer du anvisningarna i någon av följande snabbstarter:

   * [SSH/RDP över IoT Hub device strömmar med C# proxy-appar](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP över IoT Hub device strömmar med hjälp av Node.js proxy-appar](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Upprätta en SSH-session

När både de enheten och tjänsten lokala proxyservrarna har körts använder ditt SSH-klientprogram och ansluta till proxyservern service-lokala på port 2222 (i stället för SSH-daemon direkt).

```cmd/sh
ssh <username>@localhost -p 2222
```

Nu uppmanas fönstret för SSH du att ange dina autentiseringsuppgifter.

Följande bild visar konsolens utdata på enhet-lokal proxy, som ansluter till SSH-daemon på `IP_address:22`:

![Enhet-lokal proxy-utdata](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Följande bild visar konsolens utdata av SSH-klientprogram. SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, som service-lokal proxy lyssnar på:

![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du ställa in en IoT-hubb, registrerade en enhet, distribueras enhet- och ett tjänst-lokal proxy-program att upprätta en enhet stream via IoT Hub och används proxyservrarna till tunnel för SSH-trafik.

Mer information om enheten strömmar finns:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
