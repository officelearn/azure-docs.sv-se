---
title: Självstudie – ansluta en allmän Node.js klient-app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör en Node.js-klient till ditt Azure IoT Central-program. Du skapar en enhets mall genom att importera en enhets kapacitets modell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: 08df3bce9d1ecce4d4b0cdfc3034355feef6a4ba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003380"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Självstudie: skapa och ansluta ett klient program till ditt Azure IoT Central-program (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett Node.js klient program till ditt Azure IoT Central-program. Node.js programmet simulerar beteendet för en miljö sensor enhet. Du kan använda ett exempel på _enhets kapacitets modell_ för att skapa en _enhets mal len_ i IoT Central. Du lägger till vyer i enhets mal len för att låta en operatör interagera med en enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera en enhets kapacitets modell för att skapa en enhets mall.
> * Lägg till standard-och anpassade vyer i en enhets mall.
> * Publicera en enhets mall och Lägg till en riktig enhet i IoT Central programmet.
> * Skapa och kör Node.js enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som skickas från enheten.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa synkrona och asynkrona kommandon för att styra enheten.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade program** mal len. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md). Programmet måste ha skapats på eller efter 07/14/2020.
* En utvecklings dator med [Node.js](https://nodejs.org/) version 10.0.0 eller senare installerad. Du kan köra `node --version` på kommando raden för att kontrol lera din version. Anvisningarna i den här självstudien förutsätter att du kör **Node** -kommandot i kommando tolken i Windows. Du kan dock använda Node.js på många andra operativ system.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett Node.js klient program som ansluter till den riktiga enheten som du har lagt till i programmet. I det här Node.js programmet simuleras beteendet för en riktig enhet.

1. I din kommando rads miljö navigerar du till `environmental-sensor` mappen som du skapade tidigare.

1. Om du vill initiera Node.js-projektet och installera de nödvändiga beroendena, kör du följande kommandon – Godkänn alla standard alternativ när du kör `npm init` :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Skapa en fil med namnet **environmentalSensor.js** i `environmental-sensor` mappen.

1. Lägg till följande- `require` instruktioner i början av **environmentalSensor.js** -filen:

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

    Uppdatera plats hållarna `{your Scope ID}` , `{your Device ID}` och `{your Primary Key}` med de värden som du antecknade tidigare. I det här exemplet initierar du `targetTemperature` till noll, du kan använda den aktuella läsningen från enheten eller ett värde från enheten.

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

    Namnen på telemetri-objekten ( `temp` och `humid` ) måste matcha namnen som används i enhets mal len.

1. Om du vill skicka enhetens dubbla egenskaper till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central använder enheten för att synkronisera egenskaps värden mellan enheten och IoT Central programmet. Enhetens egenskaps värden använder enhetens dubbla rapporterade egenskaper. Skrivbara egenskaper använder både enhetens dubbla rapporter och önskade egenskaper.

1. Lägg till följande kod för att definiera och hantera de skrivbara egenskaper som enheten svarar på. Meddelandet som enheten skickar som svar på uppdatering av [skrivbara egenskaper](concepts-telemetry-properties-commands.md#writeable-property-types) måste innehålla `av` `ac` fälten och. `ad`Fältet är valfritt:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    När operatorn ställer in en skrivbar egenskap i IoT Central-programmet använder programmet en enhet med dubbla önskade egenskaper för att skicka värdet till enheten. Enheten svarar sedan med en enhets dubbla rapporterad egenskap. När IoT Central tar emot det rapporterade egenskap svärdet uppdateras egenskaps läget med statusen **synkroniserad**.

    Namnen på egenskaperna ( `name` och `brightness` ) måste matcha namnen som används i enhets mal len.

1. Lägg till följande kod för att hantera de kommandon som skickas från IoT Central-programmet:

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

    Namnen på kommandona ( `blink` , `turnon` , `turnoff` och `rundiagnostics` ) måste matcha namnen som används i enhets mal len.

    För närvarande använder IoT Central inte det svars schema som definierats i enhetens kapacitets modell. För ett synkront kommando kan svars nytto lasten vara vilken giltig JSON som helst. För ett asynkront kommando ska enheten returnera ett 202-svar omedelbart följt av den rapporterande egenskaps uppdateringen när arbetet är klart. Formatet för den rapporterade egenskaps uppdateringen är:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    En operatör kan visa svars nytto lasten i kommando historiken.

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
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
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

Starta enhets klient programmet genom att köra följande kommando i din kommando rads miljö:

```cmd/sh
node environmentalSensor.js
```

Du kan se att enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klient programmet](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Du kan se hur enheten svarar på kommandon och egenskaps uppdateringar:

![Observera klient programmet](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Visa rå data

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Nästa steg

Som en enhets utvecklare har du nu lärt dig grunderna för hur du skapar en enhet med hjälp av Node.js, men vissa föreslagna nästa steg är att:

* Lär dig hur du ansluter en riktig enhet till IoT Central i [ansluta en MXChip IoT DevKit-enhet till Azure IoT Central Application](./howto-connect-devkit.md) instruktion-artikeln.
* Läs [Vad är enhets mallar?](./concepts-device-templates.md) om du vill lära dig mer om rollen hets mallar när du implementerar din enhets kod.
* Läs [bli ansluten till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.

Om du föredrar att fortsätta med en uppsättning IoT Central själv studie kurser och lära dig mer om hur du skapar en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)
