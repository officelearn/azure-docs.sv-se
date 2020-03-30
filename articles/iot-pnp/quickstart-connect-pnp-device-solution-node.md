---
title: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning | Microsoft-dokument
description: Använd Node.js för att ansluta till och interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550748"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Snabbstart: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din lösning (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview förenklar IoT genom att du kan interagera med en enhets funktioner utan att du känner till den underliggande enhetsimplementeringen. Den här snabbstarten visar hur du använder Node.js för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du Node.js på din utvecklingsmaskin. Du kan hämta den senaste rekommenderade versionen för flera plattformar från [nodejs.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempelenheten

I den här snabbstarten använder du en exempelmiljösensor som skrivs i Node.js som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exemplen för Node.js](https://github.com/azure-samples/azure-iot-samples-node) GitHub-databasen till den här platsen:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Detta terminalfönster kommer nu att användas som _din enhetsterminal._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _enhetens anslutningssträng:_

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör exemplet med följande kommando:

    ```cmd/sh
    node sample_device.js
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och anmält sig själv online. Detta indikerar att enheten har börjat skicka telemetridata till navet och är nu redo att ta emot kommandon och egenskapsuppdateringar. Stäng inte terminalen, du behöver den senare för att bekräfta att serviceproverna också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempellösningen

I den här snabbstarten använder du en prov-IoT-lösning i Node.js för att interagera med exempelenheten.

1. Öppna ett annat terminalfönster (detta kommer att vara din _serviceterminal)._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-node/digital-twins/Quickstarts/Service.** Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _anslutningssträngen för IoT-hubb_ så att tjänsten kan ansluta till den:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen såg du följande meddelande som anger dess onlinestatus:

    ```cmd/sh
    reported state property as online
    ```

1. Öppna filen **get_digital_twin.js**i mappen **/azure-iot-samples-node/digital-twins/Quickstarts/Service** . Ersätt `<DEVICE_ID_GOES_HERE>` platshållaren med enhets-ID:et och spara filen.

1. Gå till _serviceterminalen_ och använd följande kommando för att köra exemplet för att läsa enhetsinformation:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Bläddra till komponenten i `environmentalSensor` serviceterminalutdata. _service_ Du ser `state` att fastigheten har rapporterats som _online:_

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

1. Öppna filen **update_digital_twin_property.js**.

1. I början av filen finns det en uppsättning konstanter som definierats med versaler. Ersätt `<DEVICE_ID_GOES_HERE>` platshållaren med ditt faktiska enhets-ID, uppdatera de återstående konstanterna med följande värden och spara filen:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Gå till _serviceterminalen_ och använd följande kommando för att köra exemplet för att uppdatera egenskapen:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. _Serviceterminalutdata_ visar den uppdaterade enhetsinformationen. Bläddra till `environmentalSensor` komponenten för att se det nya ljusstyrvvärdet 42.

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

1. Gå till _enhetsterminalen_ visas när enheten har fått uppdateringen:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Gå tillbaka till _din serviceterminal_ och kör kommandot nedan för att hämta enhetsinformationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. I _service_ serviceterminalutdata, `environmentalSensor` under komponenten, ser du att det uppdaterade ljusstyrhetsvärdet har rapporterats. Det kan ta ett tag innan enheten slutför uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskapsuppdateringen.
    
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

1. Öppna filen **invoke_command.js**.

1. I början av filen ersätter du `<DEVICE_ID_GOES_HERE>` platshållaren med ditt faktiska enhets-ID. Uppdatera de återstående konstanterna med följande värden och spara sedan filen:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Gå till _service_ serviceterminalen. Använd följande kommando för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    node invoke_command.js
    ```

1. Utdata i _serviceterminalen_ bör visa följande bekräftelse:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Gå till _enhetsterminalen,_ du ser kommandot har bekräftats:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här: Ansluta till och interagera med en enhet](howto-develop-solution.md)
