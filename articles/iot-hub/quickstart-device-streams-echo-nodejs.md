---
title: Kommunicera till enhetsapp i Node.js med Azure IoT Hub-enhetsströmmar
description: I den här snabbstarten kör du ett Node.js-program på servicesidan som kommunicerar med en IoT-enhet via en enhetsström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 0757c5eb8639e4a864b049adc92c97a7cf69adba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675508"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i Node.js via IoT Hub-enhetsströmmar (förhandsgranskning)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

I den här snabbstarten kör du ett tjänstprogram och konfigurerar kommunikation mellan en enhet och tjänst med hjälp av enhetsströmmar. Azure IoT Hub-enhetsströmmar gör det möjligt för tjänst- och enhetsprogram att kommunicera på ett säkert och brandväggsvänligt sätt. Under offentlig förhandsversion stöder Node.js SDK endast enhetsströmmar på tjänstsidan. Därför omfattar den här snabbstarten bara instruktioner för att köra programmet på tjänstsidan.

## <a name="prerequisites"></a>Krav

* Slutförande av [Kommunicera med enhetsappar i C via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-c.md) eller [Kommunicera med enhetsappar i C# via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-csharp.md).

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

* [Ett exempel nod.js-projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

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

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IoT-tillägget lägger till IoT Hub-, IoT Edge- och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDevice:** Detta är namnet på enheten du registrerar. Det rekommenderas att använda **MyDevice** som visas. Om du väljer ett annat namn för enheten måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Du måste också ha en *tjänstanslutningssträng* för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade serviceanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

I det här avsnittet kör du både enhetsprogrammet och tjänstprogrammet och kommunicerar mellan de två.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. Använd ett av de medföljande enhetsprogram som finns tillgängliga i de här snabbstarterna för ett program på enhetssidan:

* [Kommunicera med enhetsappar i C via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-c.md)

* [Kommunicera med enhetsappar i C# via IoT Hub-enhetsströmmar](./quickstart-device-streams-echo-csharp.md)

Se till att programmet på enhetssidan körs innan du fortsätter till nästa steg.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Node.js-programmet på tjänstsidan i den här snabbstarten har följande funktioner:

* Skapar en enhetsström till en IoT-enhet.
* Läser indata från kommandoraden och skickar dem till enhetsprogrammet, som returnerar dem.

Koden visar initieringsprocessen för en enhetsström, samt hur du använder den för att skicka och ta emot data.

Om du antar att programmet på enhetssidan körs följer du stegen nedan i ett lokalt terminalfönster för att köra programmet på servicesidan i Node.js:

* Ange autentiseringsuppgifter för tjänsten och enhets-ID som miljövariabler.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändra platshållaren ServiceConnectionString så att den matchar din tjänstanslutningssträng och **MyDevice** så att den matchar enhets-ID:n om du gav ditt ett annat namn.

* Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör exempelkoden med hjälp av noden.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet läser serviceprogrammet `stdin` helt enkelt på terminalen och skickar det till enheten, som sedan kommer att eka tillbaka. Detta demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen.

![Utskrift på servicesidan](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Du kan sedan avsluta programmet genom att trycka på Retur igen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, upprättade en enhetsström mellan program på enhets- och tjänstsidan och använde dataströmmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md) 