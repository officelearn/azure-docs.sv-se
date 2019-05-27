---
title: C-snabbstart för Azure IoT Hub-enhetsströmmar för SSH/RDP (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett C-exempelprogram som fungerar som proxy för att aktivera SSH/RDP-scenarier via IoT Hub device strömmar.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: ae5db52d7ac00080c2a740820debe6384cfa8dff
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872657"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-c-proxy-application-preview"></a>Snabbstart: SSH/RDP över en IoT Hub device-dataström med hjälp av C-proxyprogrammet (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Se [exempelsida för lokal Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) en översikt över installationen.

I det här dokumentet beskrivs konfigurationen för att händelsedirigera SSH-trafik nedåt (genom port 22) via enhetsströmmar. Konfigurationen för RDP-trafik är liknande och kräver en enkel konfigurationsändring. Eftersom enhetsströmmar är program- och protokolloberoende kan den aktuella snabbstarten ändras (genom att kommunikationsportarna ändras) med avseende på andra typer av programtrafik.

## <a name="how-it-works"></a>Hur det fungerar

Installationen av hur enheten och tjänsten lokal proxy-program aktiverar slutpunkt till slutpunkt-anslutning mellan SSH-klient och SSH-daemon processer ser i bilden nedan. Under den allmänna förhandsversionen stöder C SDK endast enhetsströmmar på enhetssidan. Därför omfattar den här snabbstarten bara instruktioner för att köra enhetslokalt proxyprogram. Du bör köra något av följande på tjänstsidan snabbstarter:

* [SSH/RDP över IoT Hub device strömmar med C# proxy](./quickstart-device-streams-proxy-csharp.md)

* [SSH/RDP över IoT Hub device strömmar med NodeJS proxy](./quickstart-device-streams-proxy-nodejs.md).

![Installationen av lokal webbprogramproxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Service-lokal proxy ansluter till IoT hub och startar en enhet ström till målenheten.

2. Enhetslokal proxy slutför handskakningen för ströminitiering och upprättar en slutpunkt till slutpunkt-strömningstunnel via IoT-hubbens slutpunkt för direktuppspelning till tjänstsidan.

3. Proxy för lokal enhet ansluter till SSH-daemon (SSHD) lyssnar på port 22 på enheten (Detta kan konfigureras, enligt beskrivningen i den [ *kör den lokala enheten proxyprogrammet* avsnittet](#run-the device-local-proxy-application)).

4. Service-lokal proxy väntar på dig för nya SSH-anslutningar från användaren genom att lyssna på en avsedda port som i det här fallet är port 2222 (Detta kan också konfigureras, enligt beskrivningen i den [kör enhet-lokal proxy-program i](#run-the-device-local-proxy-application). När användaren ansluter via SSH-klient gör tunneln så att SSH-programtrafik kan överföras mellan SSH-klienten och serverprogram.

> [!NOTE]
> SSH-trafik som skickas via en enhetsström dirigeras via IoT-hubbens slutpunkt för direktuppspelning i stället för att skickas direkt mellan tjänst och enhet. Mer information finns i om den [fördelarna med att använda Iot Hub device strömmar](iot-hub-device-streams-overview.md#benefits). Bilden visar dessutom hur SSH-daemon körs på samma enhet (eller dator) som den enhetslokala proxyn. I den här snabbstarten blir det tack vare att IP-adressen för SSH-daemon tillhandahålls dessutom möjligt för enhetslokal proxy och daemon att köras på olika datorer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Förhandsgranskning av enheten strömmar är för närvarande stöds endast för IoT-hubbar som har skapats i följande regioner:

   * **USA, centrala**

   * **USA, centrala – EUAP**

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med den [”utveckling för stationära datorer med C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbetsbelastning aktiverat.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).

* Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) för vissa kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Den här snabbstarten du ska använda den [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). Förbereder du en utvecklingsmiljö som används för att klona och skapa den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:n på GitHub inkluderar den exempelkod som används i den här snabbstarten. 


1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    
   ```
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
   ```

   Den här åtgärden kan förväntas ta flera minuter att slutföra.

3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

   ```
   cd azure-iot-sdk-c
   mkdir cmake
   cd cmake
   ```

4. Kör följande kommandon från den `cmake` directory för att skapa en version av SDK som är specifika för din utvecklingsplattform för klienten.

   * I Linux:

      ```bash
      cmake ..
      make -j
      ```

   * I Windows, kör du följande kommandon i Kommandotolken för utvecklare för Visual Studio 2015 eller 2017. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen.

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

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyDevice såsom det visas. Om du väljer ett annat namn för din enhet, måste du också använda det namnet i den här artikeln och uppdatera namnet i exempelprogrammen innan du kör dem på enheten.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta den _enhetsanslutningssträngen_ för enheten som du registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet ska upprätta du en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

1. Redigera källfilen `iothub_client_c2d_streaming_proxy_sample.c` i mappen `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/` och tillhandahåller enhetens anslutningssträng, målenheten IP/värdnamn och SSH-porten 22:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Kompilera exemplet:

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

3. Kör det kompilerade programmet på enheten:

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

Enligt beskrivningen i den [hur det fungerar avsnittet](#how-it-works), upprätta en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik kräver en lokal proxy i slutet (både på tjänsten och enheten). Allmänt tillgängliga förhandsversionen stöder IoT Hub C SDK endast enheten strömmar på enheten. Om du vill skapa och köra service-lokal proxy, följer du stegen tillgängligt för att köra service-lokal proxy i något av följande snabbstarter:

   * [SSH/RDP över IoT Hub device strömmar med C# proxy-appar](./quickstart-device-streams-proxy-csharp.md)

   * [SSH/RDP över IoT Hub device strömmar med hjälp av Node.js-appar för programproxy](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Upprätta en SSH-session

När både de enheten och tjänsten lokala proxyservrarna har körts använder ditt SSH-klientprogram och ansluta till proxyservern service-lokala på port 2222 (i stället för SSH-daemon direkt).

```cmd/sh
ssh <username>@localhost -p 2222
```

Nu visas med uppmaning om användarinloggning för SSH kan ange dina autentiseringsuppgifter.

Konsolens utdata på den enhetslokala proxy som ansluter till SSH-daemon på `IP_address:22`: ![Enhet-lokal proxy-utdata](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Konsolens utdata i programmet för SSH-klient (SSH-klient som kommunicerar med SSH-daemon genom att ansluta till port 22, som service-lokal proxy lyssnar på): ![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, distribuerat ett program med enhetslokal proxy och tjänstlokal proxy för att upprätta en enhetsström via IoT Hub samt använt proxyservrarna till att dirigera SSH-trafik.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
