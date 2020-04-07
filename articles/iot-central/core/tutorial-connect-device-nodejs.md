---
title: Självstudiekurs - Anslut en allmän Node.js-klientapp till Azure IoT Central | Microsoft-dokument
description: Den här självstudien visar hur du som enhetsutvecklare ansluter en enhet som kör en Node.js-klientapp till ditt Azure IoT Central-program. Du skapar en enhetsmall genom att importera en enhetskapacitetsmodell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b7aebb5b9c1aa6566cedda869f97f2d1aa20df83
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673959"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Självstudiekurs: Skapa och ansluta ett Node.js-klientprogram till ditt Azure IoT Central-program (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Den här självstudien visar hur du som enhetsutvecklare ansluter ett Node.js-klientprogram till ditt Azure IoT Central-program. Node.js-programmet simulerar beteendet hos en miljösensorenhet. Du använder en _exempelenhetsfunktionsmodell_ för att skapa en _enhetsmall_ i IoT Central. Du lägger till vyer i enhetsmallen så att en operatör kan interagera med en enhet.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Importera en enhetsfunktionsmodell för att skapa en enhetsmall.
> * Lägg till standardvyer och anpassade vyer i en enhetsmall.
> * Publicera en enhetsmall och lägg till en riktig enhet i ditt IoT Central-program.
> * Skapa och kör nod.js-enhetskoden och se den ansluta till ditt IoT Central-program.
> * Visa den simulerade telemetrin som skickas från enheten.
> * Använd en vy för att hantera enhetsegenskaper.
> * Anropa synkrona och asynkrona kommandon för att styra enheten.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade programmallen.** Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En utvecklingsmaskin med [Node.js](https://nodejs.org/) version 10.0.0 eller senare installerad. Du kan `node --version` köra på kommandoraden för att kontrollera din version. Instruktionerna i den här självstudien förutsätter att du kör **nodkommandot** i Kommandotolken i Windows. Du kan dock använda Node.js på många andra operativsystem.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett Node.js-klientprogram som ansluter till den verkliga enheten som du har lagt till i programmet. Det här nod.js-programmet simulerar beteendet hos en riktig enhet.

1. I kommandoradsmiljön navigerar `environmental-sensor` du till mappen som du skapade tidigare.

1. Om du vill initiera node.js-projektet och installera de nödvändiga beroendena kör du `npm init`följande kommandon – accepterar alla standardalternativ när du kör:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Skapa en fil som heter **environmentalSensor.js** i `environmental-sensor` mappen.

1. Lägg till `require` följande satser i början av **filen environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Lägg till följande variabeldeklarationer i filen:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Uppdatera platshållarna `{your Scope ID}` `{your Device ID}`och `{your Primary Key}` med de värden som du har noterat tidigare. I det här exemplet `targetTemperature` initierar du till noll, du kan använda den aktuella avläsningen från enheten eller ett värde från enhetstvillingen.

1. Om du vill skicka simulerad telemetri till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Namnen på telemetriobjekten`temp` ( `humid`och ) måste matcha de namn som används i enhetsmallen.

1. Om du vill skicka enhetstvillingegenskaper till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central använder enhetstvillingar för att synkronisera egenskapsvärden mellan enheten och IoT Central-programmet. Enhetsegenskapsvärden använder enhetstvillingrapporterade egenskaper. Skrivbara egenskaper använder både enhetstvillingens rapporterade och önskade egenskaper.

1. Om du vill definiera och hantera de skrivbara egenskaper som enheten svarar på lägger du till följande kod:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    När operatören anger en skrivbar egenskap i IoT Central-programmet använder programmet en enhetstvilling önskad egenskap för att skicka värdet till enheten. Enheten svarar sedan med hjälp av en enhetstvillingrapporterad egenskap. När IoT Central tar emot det rapporterade egenskapsvärdet uppdateras egenskapsvyn med statusen **synkroniserad**.

    Namnen på egenskaperna`name` ( `brightness`och ) måste matcha de namn som används i enhetsmallen.

1. Lägg till följande kod för att hantera kommandon som skickats från IoT Central-programmet:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    Namnen på kommandona`blink`( `turnon` `turnoff`, `rundiagnostics`, och ) måste matcha de namn som används i enhetsmallen.

    För närvarande använder IoT Central inte det svarsschema som definierats i enhetskapacitetsmodellen. För ett synkront kommando kan svarsnyttolasten vara vilken giltig JSON som helst. För ett asynkront kommando bör enheten returnera ett 202-svar omedelbart, följt av rapporterad egenskapsuppdatering när arbetet är klart. Formatet för den rapporterade egenskapsuppdateringen är:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    En operatör kan visa svarsnyttolasten i kommandohistoriken.

1. Lägg till följande kod för att slutföra anslutningen till Azure IoT Central och koppla samman funktionerna i klientkoden:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Kör Node.js-programmet

Om du vill starta enhetsklientprogrammet kör du följande kommando i kommandoradsmiljön:

```cmd/sh
node environmentalSensor.js
```

Du kan se enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klientprogrammet](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Du kan se hur enheten svarar på kommandon och egenskapsuppdateringar:

![Observera klientprogrammet](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om enhetskapacitetsmodeller och hur du skapar egna enhetsmallar fortsätter du till instruktioner:

> [!div class="nextstepaction"]
> [Definiera en ny IoT-enhetstyp](./howto-set-up-template.md)
