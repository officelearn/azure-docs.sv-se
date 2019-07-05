---
title: Azure IoT Hub device strömmar Snabbstart för Node.js för SSH och RDP (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett Node.js-program som fungerar som proxy för att aktivera SSH och RDP-scenarier via IoT Hub device strömmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445983"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP över en ström för IoT Hub-enhet med hjälp av en Node.js-proxyprogrammet (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. 

Den här snabbstarten beskriver körningen av en proxy Node.js-program som körs på tjänstsidan så att Secure Shell (SSH) och Remote Desktop Protocol (RDP) trafik kan skickas till enheten via en dataström med enheten. En översikt över installationen finns i [lokal Proxy-exemplet](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Node.js SDK stöder enheten strömmar på serversidan bara för allmänt tillgängliga förhandsversionen. Den här snabbstarten beskrivs därför instruktionerna för att köra service-lokal proxyprogrammet. För att köra enheten-lokal proxy-program, se:  

   * [Aktivera SSH och RDP över strömmar för IoT Hub-enhet med hjälp av en proxy C-program](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP över strömmar för IoT Hub-enhet med hjälp av en C# proxyprogrammet](./quickstart-device-streams-proxy-csharp.md)

Den här artikeln beskriver inställningarna för SSH (med hjälp av port 22) och beskriver hur du ändrar inställningarna för RDP (som använder port 3389). Eftersom enheten strömmar och protocol-programoberoende, kan du ändra samma prov för att hantera andra typer av trafik för klient-server-program, vanligtvis genom att ändra kommunikationsporten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av enheten strömmar stöds för närvarande endast för IoT-hubbar som har skapats i följande regioner:

  * Centrala USA
  * USA, centrala – EUAP

* För att köra service-lokala programmet i den här snabbstarten, behöver du Node.js v10.x.x eller senare på utvecklingsdatorn.
  * Ladda ned [Node.js](https://nodejs.org) för flera plattformar.
  * Kontrollera den aktuella versionen av Node.js på en utvecklingsdator med hjälp av följande kommando:

   ```
   node --version
   ```

* Lägg till Azure IoT-tillägget för Azure CLI till din Cloud Shell-instans genom att köra följande kommando. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS)-specifika kommandon för att Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Om du inte redan gjort det, [ladda ned exempelprojektet för Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetens identitet, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.
   > * Använd *MyDevice*, som visas. Det är det namn du angav för den registrerade enheten. Om du väljer ett annat namn för din enhet kan använda det namnet i den här artikeln och uppdatera namnet på enheten i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Om du vill aktivera backend-programmet att ansluta till din IoT-hubb och hämta meddelanden, måste du också en *tjänstanslutningssträngen*. Följande kommando hämtar anslutningssträngen för IoT-hubben:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Observera det returnerade värdet för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet ska upprätta du en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Såsom nämnts tidigare IoT Hub Node.js SDK har stöd för enheten strömmar på serversidan endast. För enhet-lokala program, använder du en enhet proxy-program som är tillgängligt på något av följande snabbstarter:

   * [Aktivera SSH och RDP över strömmar för IoT Hub-enhet med hjälp av en proxy C-program](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP över strömmar för IoT Hub-enhet med hjälp av en C# proxyprogrammet](./quickstart-device-streams-proxy-csharp.md) 

Se till att enheten-lokal proxy-program körs innan du fortsätter till nästa steg.

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Med enheten-lokal proxy-program som körs, kör du det service-lokal proxy-program som är skriven i Node.js genom att göra följande:

1. För miljövariabler, anger du dina autentiseringsuppgifter för tjänsten, mål-enhets-ID där SSH-daemon körs och portnumret för proxyservern som körs på enheten.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Ändra till föregående värden som matchar din enhet-ID och anslutningssträngen.

1. Gå till den *Snabbstarter/enhet-strömmar-tjänst* katalogen i mappen uppzippade projektet och kör service-lokal proxyprogrammet.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar

Kör SSH i Linux, med hjälp av `ssh $USER@localhost -p 2222` på en terminal. I Windows, använder du din favorit SSH-klient (till exempel PuTTY).

Konsolens utdata på service-lokala när SSH-sessionen har upprättats (de service-lokal proxy program lyssnar på port 2222):

![SSH terminal utdata](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konsolens utdata för SSH-klientprogram (SSH-klient som kommunicerar med SSH-daemon genom att ansluta till port 22, som service-lokal proxy-program lyssnar på):

![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd RDP klientprogrammet och ansluta till Tjänstproxyservern på port 2222, en godtycklig port som du valde tidigare.

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![RDP-klient ansluter till tjänsten-lokal proxyprogrammet](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du ställa in en IoT-hubb, registrerade en enhet och distribuerat en proxy-program för tjänsten så att RDP och SSH på en IoT-enhet. RDP och SSH-trafik ska dirigeras via en enhet stream via IoT hub. Den här processen eliminerar behovet för direkt anslutning till enheten.

Mer information om enheten strömmar finns:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
