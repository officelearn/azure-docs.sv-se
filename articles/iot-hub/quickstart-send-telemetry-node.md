---
title: 'Snabbstart: Skicka telemetri till Azure IoT (Node. js)'
description: I den här snabbstarten kör du två Node.js-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 92d6af41e55429f1b788de68940bc9b033c51ad6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167038"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läsa den med ett serverdelsprogram (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder två färdiga Node.js-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med hubben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

De två exempelprogram som du kör i den här snabbstarten är skrivna i Node.js. Du behöver Node. js v10. x. x eller senare på din utvecklings dator.

Du kan ladda ned Node.js för flera plattformar från [nodejs.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned exempelprojektet för Node.js från https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

   **MyNodeDevice**: Detta är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyNodeDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

1. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten. Den här tjänst anslutnings strängen skiljer sig från den enhets anslutnings sträng som du antecknade i föregående steg.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Öppna ditt lokala terminalfönster och navigera till Node.js-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.js** i en valfri textredigerare.

    Ersätt värdet för variabeln `connectionString` med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice. js**.

1. Installera de lokala bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

1. Öppna ett annat lokalt terminalfönster och navigera till Node.js-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

1. Öppna filen **ReadDeviceToCloudMessages.js** i en valfri textredigerare.

    Ersätt värdet för variabeln `connectionString` med tjänst anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **ReadDeviceToCloudMessages. js**.

1. Installera de bibliotek som krävs för serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Kör serverdelsprogrammet](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med hjälp av ett Node. js-program och läser Telemetrin från hubben med hjälp av ett enkelt Server dels program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT Hub](quickstart-control-device-node.md)
