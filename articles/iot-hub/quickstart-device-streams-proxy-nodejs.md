---
title: Azure IoT Hub-enheten strömmar Node.js snabbstart för SSH och RDP
description: I den här snabbstarten kör du ett exempel nod.js-program som fungerar som en proxy för att aktivera SSH- och RDP-scenarier över IoT Hub-enhetsströmmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675447"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP via en IoT Hub-enhetsström med hjälp av ett nod.js-proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

I den här snabbstarten aktiverar du att fjärråtkomsts- och fjärrskrivbordsprotokoll (Remote Desktop Protocol) skickas till enheten via en enhetsström. Azure IoT Hub-enhetsströmmar gör det möjligt för tjänst- och enhetsprogram att kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstarten beskriver körningen av ett Node.js proxyprogram som körs på tjänstsidan. Under offentlig förhandsversion stöder Node.js SDK endast enhetsströmmar på tjänstsidan. Därför täcker den här snabbstarten instruktioner om att endast köra det servicelokala proxyprogrammet.

## <a name="prerequisites"></a>Krav

* Slutförande av [Aktivera SSH- och RDP-enhet via IoT Hub-enhetsströmmar med hjälp av ett C-proxyprogram](./quickstart-device-streams-proxy-c.md) eller [Aktivera SSH- och RDP över IoT Hub-enhetsströmmar med hjälp av ett C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md).

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Ett exempel nod.js-projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Du kan verifiera den aktuella versionen av Node.js på utvecklingsmaskinen med hjälp av följande kommando:

```cmd/sh
node --version
```

Microsoft Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Förhandsgranskningen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapats i följande regioner:
>
> * USA, centrala
> * Centrala USA EUAP
> * Europa, norra
> * Sydostasien
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Lägga till Azure IoT-tillägg

Lägg till Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans genom att köra följande kommando. IoT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS)-specifika kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md)kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetsidentiteten kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet du registrerar rekommenderar vi att du använder *MyDevice* som visas. Om du väljer ett annat namn för enheten använder du det namnet i den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill att backend-programmet ska kunna ansluta till IoT-hubben och hämta meddelandena behöver du också en *tjänstanslutningssträng*. Följande kommando hämtar strängen för IoT-hubben:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Observera den returnerade serviceanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en end-to-end-ström till tunnel-SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För det enhetslokala programmet använder du ett enhetsproxyprogram som är tillgängligt i någon av följande snabbstarter:

   * [Aktivera SSH- och RDP över IoT Hub-enhetsströmmar med hjälp av ett C-proxyprogram](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH- och RDP över IoT Hub-enhetsströmmar med hjälp av ett C#-proxyprogram](./quickstart-device-streams-proxy-csharp.md) 

Innan du går vidare till nästa steg kontrollerar du att det enhetslokala proxyprogrammet körs. En översikt över installationen finns i [Exempel på lokal proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

I den här artikeln beskrivs inställningarna för SSH (med port 22) och beskrivs sedan hur du ändrar inställningarna för RDP (som använder port 3389). Eftersom enhetsströmmar är program- och protokolloberoende kan du ändra samma exempel för att hantera andra typer av klient-server-programtrafik, vanligtvis genom att ändra kommunikationsporten.

När det enhetslokala proxyprogrammet körs kör du det servicelokala proxyprogrammet som skrivs i Node.js genom att göra följande i ett lokalt terminalfönster:

1. För miljövariabler anger du dina tjänstautentiseringsuppgifter, målenhets-ID där SSH-demonen körs och portnumret för proxyn som körs på enheten.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Ändra platshållaren ServiceConnectionString så att den matchar din tjänstanslutningssträng och **MyDevice** så att den matchar enhets-ID:n om du gav ditt ett annat namn.

1. Navigera till `Quickstarts/device-streams-service` katalogen i den uppackade projektmappen. Använd följande kod för att köra det servicelokala proxyprogrammet:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar

I Linux kör du `ssh $USER@localhost -p 2222` SSH med hjälp av på en terminal. I Windows använder du din favorit SSH-klient (till exempel PuTTY).

Konsolutdata på den lokala service-lokalen efter att SSH-sessionen har upprättats (det servicelokala proxyprogrammet lyssnar på port 2222):

![SSH-terminalutgång](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konsolutdata från SSH-klientprogrammet (SSH-klienten kommunicerar till SSH-demonen genom att ansluta till port 22, där det servicelokala proxyprogrammet lyssnar):

![SSH-klientutflöde](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd nu rdp-klientprogrammet och anslut till tjänstproxyn på port 2222, en godtycklig port som du valde tidigare.

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![RDP-klienten ansluter till det servicelokala proxyprogrammet](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet och distribuerade ett tjänstproxyprogram för att aktivera RDP och SSH på en IoT-enhet. RDP- och SSH-trafiken kommer att tunneleras via en enhetsström via IoT-hubben. Den här processen eliminerar behovet av direkt anslutning till enheten.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
