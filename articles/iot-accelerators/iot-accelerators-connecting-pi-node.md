---
title: Anslut Raspberry Pi till lösningen för fjärrövervakning - Node.js - Azure | Microsoft-dokument
description: Beskriver hur du ansluter en Raspberry Pi-enhet till lösningsacceleratorn för fjärrövervakning med hjälp av ett program skrivet i Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 98d947e8aabf20fbfdb192cb80c9bc881007d5da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889274"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Anslut Raspberry Pi-enheten till lösningsacceleratorn för fjärrövervakning (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här självstudien visar hur du ansluter en riktig enhet till lösningsacceleratorn för fjärrövervakning. I den här självstudien använder du Node.js, vilket är ett bra alternativ för miljöer med minimala resursbegränsningar.

Om du föredrar att simulera en enhet läser du [Skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Nödvändig maskinvara

En stationär dator som gör att du kan ansluta på distans till kommandoraden på Raspberry Pi.

[Microsoft IoT Starter Kit för Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) eller motsvarande komponenter. Den här självstudien använder följande objekt från paketet:

- Hallon Pi 3
- MicroSD-kort (med NOOBS)
- En USB Mini-kabel
- En Ethernet-kabel

### <a name="required-desktop-software"></a>Obligatorisk programvara för stationära datorer

Du behöver SSH-klienten på din stationära dator så att du kan fjärransluta till kommandoraden på Raspberry Pi.

- Windows innehåller inte en SSH-klient. Vi rekommenderar att du använder [PuTTY](https://www.putty.org/).
- De flesta Linux-distributioner och Mac OS inkluderar kommandoraden SSH-verktyget. Mer information finns i [SSH Använda Linux eller Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Krävs Raspberry Pi programvara

Om du inte redan har gjort det installerar du Node.js version 4.0.0 eller senare på din Raspberry Pi. Följande steg visar hur du installerar Node.js v6 på din Raspberry Pi:

1. Anslut till din Raspberry Pi med `ssh`. Mer information finns i [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) på [Raspberry Pi:s webbplats](https://www.raspberrypi.org/).

1. Använd följande kommando för att uppdatera raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Använd följande kommandon för att ta bort alla befintliga installationer av Node.js från raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Använd följande kommando för att hämta och installera Node.js v6 på din Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Använd följande kommando för att kontrollera att du har installerat Node.js v6.11.4 framgångsrikt:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Skapa en nod.js-lösning

Utför följande steg `ssh` med anslutningen till Raspberry Pi:

1. Skapa en `remotemonitoring` mapp som kallas i din hemmapp på Raspberry Pi. Navigera till den här mappen på kommandoraden:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Om du vill hämta och installera de paket du behöver för att slutföra exempelappen kör du följande kommandon:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Skapa `remotemonitoring` en fil som heter **remote_monitoring.js**i mappen . Öppna den här filen i en textredigerare. På Raspberry Pi kan du `nano` `vi` använda eller textredigerare.

1. Lägg till följande `require` satser i filen **remote_monitoring.js:**

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Lägg till följande variabeldeklarationer efter `require`-instruktionerna. Ersätt platshållarvärdet `{device connection string}` med värdet som du har noterat för den enhet som du har etablerat i lösningen för fjärrövervakning:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Om du vill definiera vissa bastelemetridata lägger du till följande variabler:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Om du vill definiera vissa egenskapsvärden lägger du till följande variabler:

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

1. Lägg till följande variabel för att definiera de rapporterade egenskaperna som ska skickas till lösningen. Dessa egenskaper omfattar metadata som ska visas i webbgränssnittet:

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

1. Om du vill skriva ut åtgärdsresultat lägger du till följande hjälpfunktion:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Lägg till följande hjälpfunktion som ska användas för att randomisera telemetrivärdena:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Lägg till följande allmänna funktion för att hantera direkta metodanrop från lösningen. Funktionen visar information om den direkta metod som anropades, men i det här exemplet ändras inte enheten på något sätt. Lösningen använder direkta metoder för att agera på enheter:

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

1. Lägg till följande funktion för att hantera **firmwareUpdate** direkt metodanrop från lösningen. Funktionen verifierar de parametrar som skickas i den direkta metoden nyttolast och sedan asynkront kör en firmware uppdatering simulering:

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

1. Lägg till följande funktion för att simulera ett tidskrävande uppdateringsflöde för inbyggd programvara som rapporterar förloppet tillbaka till lösningen:

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

1. Lägg till följande kod för att skicka telemetridata till lösningen. Klientappen lägger till egenskaper i meddelandet för att identifiera meddelandeschemat:

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

1. Lägg till följande kod för att skapa en klientinstans:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Lägg till följande kod i:

    * Öppna anslutningen.
    * Ställ in en hanterare för önskade egenskaper.
    * Skicka rapporterade egenskaper.
    * Registrera hanterare för de direkta metoderna. I exemplet används en separat hanterare för den direkta metoden för uppdatering av inbyggd programvara.
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

1. Spara ändringarna i **filen remote_monitoring.js.**

1. Om du vill starta exempelprogrammet kör du följande kommando i kommandotolken på Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
