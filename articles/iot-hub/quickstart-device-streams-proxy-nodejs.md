---
title: Azure IoT Hub enhets strömmar Node. js snabb start för SSH och RDP (för hands version) | Microsoft Docs
description: I den här snabb starten kör du ett exempel-Node. js-program som fungerar som en proxy för att aktivera SSH-och RDP-scenarier över IoT Hub enhets strömmar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445983"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP över en IoT Hub enhets ström med hjälp av ett Node. js-proxyprogram (för hands version)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enhets strömmar som en förhands [gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. 

Den här snabb starten beskriver körningen av ett Node. js-proxyprogram som körs på tjänst sidan för att aktivera SSH (Secure Shell) och Remote Desktop Protocol (RDP) trafik som ska skickas till enheten via en enhets ström. En översikt över installationen finns i exempel på [lokal Proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

I den offentliga för hands versionen stöder Node. js SDK endast enhets strömmar på tjänst sidan. Därför täcker den här snabb starten instruktioner för att bara köra det tjänst lokala proxy-programmet. Information om hur du kör programmet för enhets lokala proxy finns i:  

   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp av ett C proxy-program](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp C# av ett proxy-program](./quickstart-device-streams-proxy-csharp.md)

I den här artikeln beskrivs inställningarna för SSH (med port 22) och hur du ändrar inställningarna för RDP (som använder port 3389). Eftersom enhets strömmar är program-och protokoll-oberoende kan du ändra samma exempel för att hantera andra typer av program trafik på klient servern, vanligt vis genom att ändra kommunikations porten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapas i följande regioner:

  * Centrala USA
  * USA, centrala – EUAP

* Om du vill köra det lokala programmet i den här snabb starten behöver du Node. js v10. x. x eller senare på din utvecklings dator.
  * Hämta [Node. js](https://nodejs.org) för flera plattformar.
  * Verifiera den aktuella versionen av Node. js på utvecklings datorn med hjälp av följande kommando:

   ```
   node --version
   ```

* Lägg till Azure IoT-tillägget för Azure CLI till din Cloud Shell instans genom att köra följande kommando. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT-kommandon (Device Provisioning service) i Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Om du inte redan har gjort det kan du [Hämta exempel-Node. js-projektet](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) och extrahera zip-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har [slutfört snabb starten: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Skapa enhets identiteten genom att köra följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt plats hållaren *YourIoTHubName* med det namn du väljer för din IoT Hub.
   > * Använd min *enhet*som det visas. Det är det namn som angetts för den registrerade enheten. Om du väljer ett annat namn på enheten använder du det namnet i den här artikeln och uppdaterar enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Om du vill aktivera backend-programmet för att ansluta till din IoT-hubb och hämta meddelandena måste du också ha en *anslutnings sträng för tjänsten*. Följande kommando hämtar strängen för din IoT-hubb:

   > [!NOTE]
   > Ersätt plats hållaren *YourIoTHubName* med det namn du väljer för din IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Observera det returnerade värdet för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en slutpunkt-till-slutpunkt-ström för att dirigera SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Som tidigare nämnts stöder IoT Hub Node. js SDK endast enhets strömmar på tjänst sidan. Använd ett program för enhets proxy som är tillgängligt i någon av följande snabb starter för det enhets lokala programmet:

   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp av ett C proxy-program](./quickstart-device-streams-proxy-c.md)
   * [Aktivera SSH och RDP över IoT Hub enhets strömmar med hjälp C# av ett proxy-program](./quickstart-device-streams-proxy-csharp.md) 

Innan du fortsätter till nästa steg måste du kontrol lera att programmet enhetens lokala proxy körs.

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Med det enhets lokala proxy-programmet igång kör du det tjänst lokala proxy-programmet som är skrivet i Node. js genom att göra följande:

1. För miljövariabler anger du autentiseringsuppgifterna för tjänsten, mål enhets-ID: t där SSH-daemonen körs och port numret för den proxy som körs på enheten.

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

   Ändra föregående värden så att de matchar enhets-ID och anslutnings sträng.

1. Gå till katalogen *snabb starter/Device-Streams-service* i den ej zippade projektmappen och kör det tjänst lokala proxy-programmet.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar

I Linux kör du SSH genom att `ssh $USER@localhost -p 2222` använda på en Terminal. I Windows använder du din favorit-SSH-klient (till exempel, SparaTillFil).

Konsol utdata på den lokala tjänsten efter att SSH-sessionen har upprättats (det service lokala proxy-programmet lyssnar på port 2222):

![Utdata från SSH-Terminal](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Konsol utmatningen av SSH-klientprogrammet (SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, som det lokala proxy-programmet lyssnar på):

![Utdata för SSH-klient](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd nu RDP-klientprogrammet och Anslut till Tjänstproxyn på port 2222, en godtycklig port som du valde tidigare.

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![RDP-klienten ansluter till det lokala proxy-programmet](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du konfigurerat en IoT-hubb, registrerat en enhet och distribuerat ett proxy-program för att aktivera RDP och SSH på en IoT-enhet. RDP-och SSH-trafiken dirigeras via en enhets ström via IoT Hub. Den här processen eliminerar behovet av direkt anslutning till enheten.

Mer information om enhets strömmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
