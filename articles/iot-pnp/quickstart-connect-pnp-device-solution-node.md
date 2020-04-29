---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning | Microsoft Docs
description: Använd Node. js för att ansluta till och interagera med en IoT-Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550748"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Snabb start: interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning (Node. js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt för dig att interagera med enhetens funktioner utan att du behöver känna till den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Node. js för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du Node. js på utvecklings datorn. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel miljö sensor som är skriven i Node. js som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exemplen för Node. js](https://github.com/azure-samples/azure-iot-samples-node) GitHub-lagringsplatsen till den här platsen:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Det här terminalfönstret används nu som din _enhets_ Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-Node/Digital-Twins/Quickstarts/Device** . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör exemplet med följande kommando:

    ```cmd/sh
    node sample_device.js
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Detta anger att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den senare för att bekräfta att tjänst exemplen också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel-IoT-lösning i Node. js för att interagera med exempel enheten.

1. Öppna ett annat terminalfönster (det här är din _tjänsts_ Terminal). Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-Node/Digital-Twins/Quickstarts/service** . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _IoT Hub-anslutningssträngen_ så att tjänsten kan ansluta till den:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen såg du följande meddelande som visar att statusen är online:

    ```cmd/sh
    reported state property as online
    ```

1. Öppna filen **get_digital_twin. js**i mappen **/Azure-IoT-samples-Node/Digital-Twins/Quickstarts/service** . Ersätt `<DEVICE_ID_GOES_HERE>` plats hållaren med ditt enhets-ID och spara filen.

1. Gå till _tjänsten_ Terminal och Använd följande kommando för att köra exemplet för att läsa enhets information:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Rulla till _service_ `environmentalSensor` komponenten i utdata från tjänsten. Du ser att `state` egenskapen har rapporter ATS som _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Uppdatera en skrivbar egenskap

1. Öppna filen **update_digital_twin_property. js**.

1. I början av filen finns en uppsättning konstanter definierade med plats hållare för versaler. Ersätt `<DEVICE_ID_GOES_HERE>` plats hållaren med ditt faktiska enhets-ID, uppdatera de återstående konstanterna med följande värden och spara filen:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Gå till _tjänsten_ Terminal och Använd följande kommando för att köra exemplet för att uppdatera egenskapen:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. I _tjänstens Terminal-_ utdata visas den uppdaterade enhets informationen. Bläddra till `environmentalSensor` komponenten om du vill se det nya värdet för ljus styrka på 42.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Gå till _enhetens_ Terminal. du ser att enheten har tagit emot uppdateringen:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Gå tillbaka till _tjänsten_ Terminal och kör kommandot nedan för att hämta enhets informationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. I _tjänstens_ Terminal-utdata, under `environmentalSensor` komponenten, ser du att det uppdaterade värdet för ljus styrka har rapporter ATS. Obs: det kan ta en stund innan enheten har slutfört uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskaps uppdateringen.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Öppna filen **invoke_command. js**.

1. I början av filen ersätter du `<DEVICE_ID_GOES_HERE>` plats hållaren med det aktuella enhets-ID: t. Uppdatera de återstående konstanterna med följande värden och spara sedan filen:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Gå till _tjänsten_ Terminal. Använd följande kommando för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    node invoke_command.js
    ```

1. Utdata i _tjänstens_ Terminal ska visa följande bekräftelse:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Gå till _enhetens_ Terminal. du ser att kommandot har bekräftats:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](howto-develop-solution.md)
