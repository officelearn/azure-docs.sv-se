---
title: Anslut Raspberry Pi till fjärr styrnings lösningen – Node.js – Azure | Microsoft Docs
description: Beskriver hur du ansluter en Raspberry Pi-enhet till en lösnings Accelerator för fjärrövervakning med hjälp av ett program som är skrivet i Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 46f8f811e7d1a019fa332cd289892d110531c84d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261557"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Anslut din Raspberry Pi Node.js-enhet till (Remote Monitoring Solution Accelerator)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du se hur du ansluter en riktig enhet till en lösnings Accelerator med fjärr styrning. I den här självstudien använder du Node.js, vilket är ett utmärkt alternativ för miljöer med minimala resurs begränsningar.

Om du föredrar att simulera en enhet kan du läsa [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Nödvändig maskin vara

En stationär dator så att du kan fjärrans luta till kommando raden på Raspberry Pi.

[Microsoft IoT starter kit för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. I den här självstudien används följande objekt från paketet:

- Raspberry Pi 3
- MicroSD-kort (med NOOBS)
- En USB-Mini-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Nödvändig Desktop-programvara

Du behöver SSH-klienten på din station ära dator så att du kan fjärrans luta till kommando raden på Raspberry Pi.

- Windows innehåller ingen SSH-klient. Vi rekommenderar att du använder [SparaTillFil](https://www.putty.org/).
- De flesta Linux-distributioner och Mac OS innehåller SSH-verktyget för kommando rads verktyg. Mer information finns i [SSH med Linux eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Nödvändig Raspberry Pi-programvara

Om du inte redan har gjort det installerar du Node.js version 4.0.0 eller senare på Raspberry Pi. Följande steg visar hur du installerar Node.js V6 på din Raspberry Pi:

1. Anslut till din Raspberry Pi med `ssh` . Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på [Raspberry Pi-webbplatsen](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera din Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommandon för att ta bort eventuell befintlig installation av Node.js från din Raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Använd följande kommando för att hämta och installera Node.js V6 på din Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Använd följande kommando för att kontrol lera att du har installerat Node.js v-6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Skapa en Node.js-lösning

Utför följande steg med `ssh` anslutningen till din Raspberry Pi:

1. Skapa en mapp `remotemonitoring` som heter i din arbetsmapp på Raspberry Pi. Navigera till den här mappen på kommando raden:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Om du vill hämta och installera de paket du behöver för att slutföra exempel appen kör du följande kommandon:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. I `remotemonitoring` mappen skapar du en fil med namnet **remote_monitoring.js**. Öppna den här filen i en textredigerare. I Raspberry Pi kan du använda `nano` eller `vi` text redigerare.

1. Lägg till följande-instruktioner i **remote_monitoring.js** -filen `require` :

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Lägg till följande variabeldeklarationer efter `require`-instruktionerna. Ersätt plats hållarens värde `{device connection string}` med det värde som du antecknade för den enhet som du etablerade i lösningen för fjärrövervakning:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Lägg till följande variabler för att definiera vissa grundläggande telemetridata:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Lägg till följande variabler för att definiera vissa egenskaps värden:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Lägg till följande variabel för att definiera de rapporterade egenskaper som ska skickas till lösningen. Dessa egenskaper inkluderar metadata som ska visas i webb gränssnittet:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Om du vill skriva ut åtgärds resultatet lägger du till följande hjälp funktion:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Lägg till följande hjälp funktion för att göra telemetri-värden slumpmässiga:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Lägg till följande generiska funktion för att hantera direkta metod anrop från lösningen. Funktionen visar information om den direkta metoden som anropades, men i det här exemplet ändrar inte enheten på något sätt. Lösningen använder direkta metoder för att agera på enheter:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Lägg till följande funktion för att hantera anropen av **FirmwareUpdate** Direct-metoden från lösningen. Funktionen verifierar parametrarna som skickas i nytto lasten för direkt metoden och kör sedan asynkront en uppdaterings simulering för inbyggd program vara:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Lägg till följande funktion för att simulera ett tids krävande uppdaterings flöde för inbyggd program vara som rapporterar tillbaka till lösningen:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Lägg till följande kod för att skicka telemetridata till lösningen. Klient programmet lägger till egenskaper till meddelandet för att identifiera meddelande schemat:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Lägg till följande kod för att skapa en klient instans:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Lägg till följande kod i:

    * Öppna anslutningen.
    * Konfigurera en hanterare för önskade egenskaper.
    * Skicka rapporterade egenskaper.
    * Registrera hanterare för direkta metoder. Exemplet använder en separat hanterare för direkt metoden för uppdatering av den inbyggda program varan.
    * Börja skicka telemetri.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Spara ändringarna i **remote_monitoring.jss ** filen.

1. Starta exempel programmet genom att köra följande kommando i kommando tolken på Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
