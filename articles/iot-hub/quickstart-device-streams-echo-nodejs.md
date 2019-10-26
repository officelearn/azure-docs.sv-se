---
title: Kommunicera med en enhets app i Node. js via IoT Hub enhets strömmar (förhands granskning) | Microsoft Docs
description: I den här snabb starten ska du köra ett Node. js-program på tjänst sidan som kommunicerar med en IoT-enhet via en enhets ström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4acfe148e424d41308e310abd2a10e4e21133799
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900849"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snabb start: kommunicera med ett enhets program i Node. js via IoT Hub enhets strömmar (för hands version)

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

## <a name="prerequisites"></a>Krav

För hands versionen av enhets strömmar stöds för närvarande bara för IoT-hubbar som skapats i följande regioner:

  * USA, centrala
  * Centrala USA-EUAP
  * Europa, norra
  * Asien, sydöstra

Om du vill köra programmet på tjänst sidan i den här snabb starten behöver du Node. js v10. x. x eller senare på din utvecklings dator.

Du kan hämta Node. js för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT-kommandon (Device Provisioning service) till Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned Node.js-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   Min **enhet**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder min **enhet** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Du måste också ha en *tjänstanslutningssträng* för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade tjänst anslutnings strängen för senare användning i den här snabb starten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

I det här avsnittet ska du köra både program på enhets sidan och programmet på tjänst sidan och kommunicera mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För ett program på enhets sidan använder du en av de medföljande enhets programmen som finns i följande snabb starter:

   * [Kommunicera med enhetens appar i C via IoT Hub enhets strömmar](./quickstart-device-streams-echo-c.md)

   * [Kommunicera med enhets program C# i via IoT Hub enhets strömmar](./quickstart-device-streams-echo-csharp.md)

Se till att programmet på enhetssidan körs innan du fortsätter till nästa steg.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Förutsatt att programmet på enhets sidan körs följer du stegen nedan i ett lokalt terminalfönster för att köra programmet på tjänst sidan i Node. js:

* Ange autentiseringsuppgifter för tjänsten och enhets-ID som miljövariabler.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändra plats hållaren ServiceConnectionString så att den matchar din tjänst anslutnings sträng och att **enheten** matchar ditt enhets-ID om du har gett ditt eget namn.

* Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör exempelkoden med hjälp av noden.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet läser programmet på tjänst sidan helt enkelt `stdin` på terminalen och skickar det till enheten, som sedan upprepar den igen. Detta demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen.

![Utdata för konsol på tjänst Sidan](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Du kan sedan avsluta programmet genom att trycka på Retur igen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, upprättat en enhets ström mellan program på enhets-och tjänst sidan och använde data strömmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md) 