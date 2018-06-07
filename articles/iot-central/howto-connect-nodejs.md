---
title: Ansluta ett allmänt Node.js-klientprogram till Azure IoT Central | Microsoft Docs
description: Som en enhet utvecklare, hur du ansluter en allmän Node.js-enhet till din Azure IoT centralt program.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 42ede975f2cfde2d9c0a61d15ba1af412a88c556
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628546"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Ansluta en allmän klientprogram att tillämpningsprogrammet Azure IoT Central (Node.js)

Den här artikeln beskriver hur som utvecklare som enheten kan ansluta ett allmänt Node.js-program som representerar en fysisk enhet till din Microsoft Azure IoT centralt program.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT centrala program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
1. En utvecklingsdator med [Node.js](https://nodejs.org/) version 4.0.0 eller senare installerat. Du kan köra `node --version` i kommandoraden för att kontrollera din version. Node.js är tillgängligt för många olika operativsystem.

I ditt Azure IoT centrala program behöver du en mall för enheten med följande mätningar och enhetsegenskaper definierats:

### <a name="telemetry-measurements"></a>Telemetri mått

Lägg till följande telemetri i den **mätningar** sidan:

| Visningsnamn | Fältnamn  | Enheter | Min | Max | Decimaler |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatur  | temperatur | F     | 60  | 110 | 0              |
| Fuktighet     | fuktighet    | %     | 0   | 100 | 0              |
| Tryck     | tryck    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Datatypen för måttet telemetri är double.

Ange fältnamn exakt som de visas i tabellen i mallen för enheten. Om fältnamnen inte matchar kan telemetrin inte visas i programmet.

### <a name="state-measurements"></a>Tillstånd mått

Lägg till följande tillstånd i den **mätningar** sidan:

| Visningsnamn | Fältnamn  | Värde 1 | Visningsnamn | Värde 2 | Visningsnamn |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fläktläge     | fläktläge     | 1       | Körs      | 0       | Stoppad      |

> [!NOTE]
  Datatypen för måttet tillstånd är sträng.

Ange fältnamn exakt som de visas i tabellen i mallen för enheten. Om fältnamnen inte matchar kan tillståndet inte visas i programmet.

### <a name="event-measurements"></a>Händelsen mått

Lägg till följande händelse i den **mätningar** sidan:

| Visningsnamn | Fältnamn  | Severity |
| ------------ | ----------- | -------- |
| Överhettning  | överhettas    | Fel    |

> [!NOTE]
  Datatypen för måttet händelse är sträng.

### <a name="device-properties"></a>Enhetsegenskaper

Lägg till följande egenskaper för enheter i den **egenskapssidan**:

| Visningsnamn        | Fältnamn        | Datatyp |
| ------------------- | ----------------- | --------- |
| Serienummer       | serieNummer      | text      |
| Enhetstillverkare | tillverkare      | text      |

Ange namnen på sätt som visas i tabellen i mallen för enheten. Om fältnamnen inte matchar kan inte programmet visa egenskapens värde.

### <a name="settings"></a>Inställningar

Lägg till följande **nummer** inställningar i den **inställningssidan**:

| Visningsnamn    | Fältnamn     | Enheter | Decimaler | Min | Max  | Inledande |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Fläkthastighet       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Ange temperatur | angeTemperatur | F     | 0        | 20  | 200  | 80      |

Ange fältnamn på exakt som de visas i tabellen i mallen för enheten. Om fältnamnen inte matchar inte enheten ta emot värdet.

### <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT centrala program lägger du till en verklig enhet från enheten mallen du skapar och anteckna anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet dina Azure IoT centralt program](tutorial-add-device.md)

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett klientprogram som implementerar den faktiska enheten som du lagt till programmet.

1. Skapa en mapp med namnet `connected-air-conditioner-adv` på datorn. Navigera till mappen i kommandoradsverktyget miljön.

1. Kör följande kommandon för att initiera projektet Node.js:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Skapa en fil med namnet **connectedAirConditionerAdv.js** i den `connected-air-conditioner-adv` mapp.

1. Lägg till följande `require` instruktioner i början av den **connectedAirConditionerAdv.js** fil:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Lägg till följande variabeldeklarationer i filen:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Uppdatera platshållaren `{your device connection string}` med anslutningssträngen enhet. Kopiera det här värdet från informationssidan när du har lagt till enheten verkliga. I det här exemplet vi initiera `targetTemperature` till noll, kan du om du vill göra den aktuella läsningen från enheten eller värdet från enheten dubbla. 

1. Lägg till följande funktion i filen för att skicka telemetri, tillstånd och händelsen mått till din Azure IoT centrala programmet:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Om du vill skicka enhetens egenskaper till dina Azure IoT centralt program, lägger du till följande funktion i filen:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Lägg till följande definition för att ange inställningar för enheten svarar på:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Lägg till följande i filen för att hantera uppdaterade inställningar från din Azure IoT centralt program:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Lägg till följande för att slutföra anslutningen till Azure IoT Central och koppla samman funktionerna i klientkoden:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Kör Node.js-programmet

Kör följande kommando i Kommandotolken miljön:

```cmd/sh
node connectedAirConditionerAdv.js
```

Som operatör i ditt Azure IoT centrala program för verkliga enheten kan du:

* Visa telemetrin på den **mätningar** sidan:

    ![Visa telemetrin](media/howto-connect-nodejs/viewtelemetry.png)

* Visa enheten egenskapsvärden som skickas från enheten på den **egenskaper** sidan.

    ![Visa egenskaper för enhet](media/howto-connect-nodejs/viewproperties.png)

* Ange fläkt hastighet och mål temperatur från den **inställningar** sidan.

    ![Hastighet för set-fläkt](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en allmän Node.js-klient till din Azure IoT centralt program, är här de föreslagna nästa steg:
* [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
