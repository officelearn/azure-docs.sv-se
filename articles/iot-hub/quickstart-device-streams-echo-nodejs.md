---
title: Kommunicera med en enhets app i Node. js via IoT Hub enhets strömmar (förhands granskning) | Microsoft Docs
description: I den här snabbstarten kör du ett Node.js-program på tjänstsidan som kommunicerar med en IoT-enhet via en enhetsström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: e85f2ea849aca9deeb92da7d7b2381d6c2b1b725
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802437"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i Node.js via IoT Hub-enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enhets strömmar som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Under den allmänna förhandsversionen stöder Node.js SDK endast enhetsströmmar på tjänstsidan. Därför omfattar den här snabbstarten bara instruktioner för att köra programmet på tjänstsidan. Du bör köra ett tillhör ande program på enhets sidan från någon av följande snabb starter:

* [Kommunicera med enhetens appar i C via IoT Hub enhets strömmar](./quickstart-device-streams-echo-c.md)

* [Kommunicera med enhetens appar C# i via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md).

Node.js-programmet på tjänstsidan i den här snabbstarten har följande funktioner:

* Skapar en enhetsström till en IoT-enhet.

* Läser indata från kommandoraden och skickar dem till enhetsprogrammet, som returnerar dem.

Koden visar initierings processen för en enhets ström, samt hur du kan använda den för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapats i följande regioner:

*  **USA, centrala**

*  **Centrala USA-EUAP**

För att köra programmet på tjänst sidan i den här snabb starten behöver du Node. js v10. x. x eller senare på din utvecklings dator.

Du kan hämta Node. js för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned Node.js-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Du kan hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

   **MyDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyDevice såsom det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Du måste också ha en *tjänstanslutningssträng* för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anteckna det returnerade värdet, som ser ut så här:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

I det här avsnittet ska du köra både program på enhets sidan och programmet på tjänst sidan och kommunicera mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För program på enhets sidan använder du det medföljande enhets programmet som är tillgängligt i någon av följande snabb starter:

   * [Kommunicera med enhetens appar i C via IoT Hub enhets strömmar](./quickstart-device-streams-echo-c.md)

   * [Kommunicera med enhets program C# i via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md)

Se till att programmet på enhetssidan körs innan du fortsätter till nästa steg.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Förutsatt att programmet på enhetssidan körs följer du stegen nedan för att köra programmet på tjänstsidan som skrivits i Node.js:

* Ange autentiseringsuppgifter för tjänsten och enhets-ID som miljövariabler.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändra `MyDevice` till det enhets-ID som du har valt för enheten.

* Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör exempelkoden med hjälp av noden.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet läser `stdin` programmet på tjänst sidan bara på terminalen och skickar det till enheten, som sedan skickar den tillbaka. Detta demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen.

![Utdata för konsol på tjänst Sidan](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Du kan sedan avsluta programmet genom att trycka på Retur igen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, upprättat en enhetsström mellan program på enhetssidan och tjänstsidan samt använt dataströmmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md) 