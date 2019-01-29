---
title: Node.js-snabbstart för Azure IoT Hub-enhetsströmmar för SSH/RDP (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett Node.js-exempelprogram som fungerar som proxy för att möjliggöra SSH/RDP-scenarier via IoT Hub-enhetsströmmar.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 012fdfa4faf10cacaf85819517f358c1af1ab39d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830714"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Snabbstart: SSH/RDP över IoT Hub-enhetsströmmar med hjälp av Node.js-proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. I den här snabbstarten beskrivs körningen av ett Node.js-proxyprogram som körs på tjänstsidan för att göra så att SSH- och RDP-trafik kan skickas till enheten via en enhetsström. En översikt över konfigurationen finns på [den här sidan](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). Under den allmänna förhandsversionen stöder Node.js SDK endast enhetsströmmar på tjänstsidan. Därför omfattar den här snabbstarten bara instruktioner för att köra proxyn på tjänstsidan. Du bör köra en tillhörande proxy på enhetssidan som finns tillgängligt i guiderna för [C-snabbstart](./quickstart-device-streams-proxy-c.md) eller [C#-snabbstart](./quickstart-device-streams-proxy-csharp.md).

Först beskrivs konfiguration för SSH (via port `22`). Sedan beskrivs hur du ändrar konfigurationen för RDP (som använder port 3389). Eftersom enhetsströmmar är program- och protokolloberoende kan samma exempel ändras (vanligtvis genom att kommunikationsportarna ändras) med avseende på andra typer av programtrafik.

Koden visar initiering och användning av en enhetsström och kan även återanvändas för annan programtrafik (förutom RDP och SSH).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna köra programmet på tjänstsidan i den här snabbstarten behöver du ha Node.js v4.x.x eller senare på utvecklingsdatorn.

Du kan ladda ned Node.js för flera plattformar från [nodejs.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```
node --version
```

Ladda ned Node.js-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

   **MyDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyDevice såsom det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Anteckna det returnerade värdet, som ser ut så här:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

### <a name="run-the-device-side-proxy"></a>Köra proxyn på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För programmet på enhetssidan använder du tillhörande enhetsproxyprogram som finns tillgängliga i guiderna för [C-snabbstart](./quickstart-device-streams-proxy-c.md) eller [C#-snabbstart](./quickstart-device-streams-proxy-csharp.md). Se till att proxyn på enhetssidan körs innan du fortsätter till nästa steg.


### <a name="run-the-service-side-proxy"></a>Köra proxy på tjänstsidan

Förutsatt att proxyn på enhetssidan körs följer du stegen nedan för att köra proxyn på tjänstsidan som skrivits i Node.js:

- Ange autentiseringsuppgifter för tjänsten, målets enhets-ID där SSH-daemon körs samt portnumret för den proxy som körs på enheten som miljövariabler.
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
Ändra `MyDevice` till det enhets-ID som du har valt för enheten.

- Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör den tjänstlokala proxyn.
```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH till din enhet via enhetsströmmar
I Linux kör du SSH med hjälp av `ssh $USER@localhost -p 2222` på en terminal. I Windows använder du valfri SSH-klient (till exempel PuTTY).

Konsolens utdata på tjänstsidan när SSH-sessionen har upprättats (den tjänstlokala proxyn lyssnar på port 2222): ![Alternativ text](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "Utdata för SSH-terminal")


Konsolens utdata för SSH-klientprogrammet (SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port <code>22</code> där den tjänstlokala proxyn lyssnar): ![Alternativ text](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "Utdata för SSH-klient")


### <a name="rdp-to-your-device-via-device-streams"></a>RDP till din enhet via enhetsströmmar

Använd RDP-klientprogrammet och anslut till tjänstproxyn på port 2222 (det här var en godtycklig tillgänglig port som du valde tidigare).

> [!NOTE]
> Kontrollera att din enhetsproxy är korrekt konfigurerad för RDP och konfigurerad med RDP-port 3389.

![Alternativ text](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "RDP-klient ansluter till tjänstlokal proxy.")


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet och distribuerat ett tjänstproxyprogram för att aktivera RDP och SSH till en IoT-enhet. RDP- och SSH-trafiken kommer att dirigeras genom en enhetsström via IoT Hub. Detta eliminerar behovet av en direktanslutning till enheten.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
