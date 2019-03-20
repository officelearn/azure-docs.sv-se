---
title: Node.js-snabbstart för Azure IoT Hub-enhetsströmmar (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du ett Node.js-program på tjänstsidan som kommunicerar med en IoT-enhet via en enhetsström.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1e7efe28918cafb3fa9547c144be3360768d549c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079903"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i Node.js via IoT Hub-enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Under den allmänna förhandsversionen stöder Node.js SDK endast enhetsströmmar på tjänstsidan. Därför omfattar den här snabbstarten bara instruktioner för att köra programmet på tjänstsidan. Du bör köra ett tillhörande program på enhetssidan som finns tillgängligt i guiderna för [C-snabbstart](./quickstart-device-streams-echo-c.md) eller [C#-snabbstart](./quickstart-device-streams-echo-csharp.md).

Node.js-programmet på tjänstsidan i den här snabbstarten har följande funktioner:

* Skapar en enhetsström till en IoT-enhet.

* Läser indata från kommandoraden och skickar dem till enhetsprogrammet, som returnerar dem.

Koden visar hur initiation du en enhet ström, samt hur du använder den för att skicka och ta emot data.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Förhandsgranskning av enheten strömmar är för närvarande stöds endast för IoT-hubbar som har skapats i följande regioner:

  - **USA, centrala**
  - **USA, centrala – EUAP**

För att kunna köra programmet på tjänstsidan i den här snabbstarten behöver du ha Node.js v4.x.x eller senare på utvecklingsdatorn.

Du kan ladda ned Node.js för flera plattformar från [Node.js.org](https://nodejs.org).

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
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anteckna det returnerade värdet, som ser ut så här:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Som tidigare nämnts stöder IoT Hub Node.js SDK endast enhetsströmmar på tjänstsidan. För programmet på enhetssidan använder du tillhörande enhetsprogram som finns tillgängliga i guiderna för [C-snabbstart](./quickstart-device-streams-echo-c.md) eller [C#-snabbstart](./quickstart-device-streams-echo-csharp.md). Se till att programmet på enhetssidan körs innan du fortsätter till nästa steg.


### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Förutsatt att programmet på enhetssidan körs följer du stegen nedan för att köra programmet på tjänstsidan som skrivits i Node.js:

- Ange autentiseringsuppgifter för tjänsten och enhets-ID som miljövariabler.
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  ```
  Ändra `MyDevice` till det enhets-ID som du har valt för enheten.

- Gå till `Quickstarts/device-streams-service` i den uppackade projektmappen och kör exempelkoden med hjälp av noden.
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
  ```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet läser programmet på tjänstsidan helt enkelt stdin på terminalen och skickar det till enheten, som sedan returnerar det. Detta demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen.

Konsolens utdata på tjänstsidan: ![alternativ text](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "Konsolens utdata på tjänstsidan")


Du kan sedan avsluta programmet genom att trycka på Retur igen.


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, upprättat en enhetsström mellan program på enhetssidan och tjänstsidan samt använt dataströmmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
