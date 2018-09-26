---
title: Anslut en enhet med Node.js | Microsoft Docs
description: Beskriver hur du ansluter en enhet till Azure IoT Suite och förkonfigurerade fjärrövervakningslösningen med hjälp av ett program som skrivits i Node.js.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 87a2e97638508eef1d90a219cfb38d1fcac81d55
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094501"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Anslut enheten till den förkonfigurerade lösningen för fjärrövervakning (Node.js)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Skapa en lösning för node.js-exempel

Se till att Node.js version 0.11.5 eller senare är installerat på utvecklingsdatorn. Du kan köra `node --version` på kommandoraden för att kontrollera versionen.

1. Skapa en mapp med namnet **RemoteMonitoring** på utvecklingsdatorn. Navigera till den här mappen i kommandoradsverktyget-miljön.

1. Kör följande kommandon för att ladda ned och installera paket som du behöver för att slutföra exempelappen:

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. I den **RemoteMonitoring** mapp, skapa en fil med namnet **remote_monitoring.js**. Öppna den här filen i en textredigerare.

1. I den **remote_monitoring.js** Lägg till följande `require` instruktioner:

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Lägg till följande variabeldeklarationer efter `require`-instruktionerna. Ersätt platshållarvärdena [Enhets-ID] och [Enhetsnyckel] med de värden du antecknade för enheten i instrumentpanelen för fjärrövervakningslösningen. Använd värdnamnet för IoT Hub från lösningens instrumentpanel för att ersätta [IoTHub-namn]. Om ditt värdnamn för IoT Hub exempelvis är **contoso.azure-devices.net** ersätter du [IoTHub-namn] med **contoso**:

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Lägg till följande variabler för att definiera vissa grundläggande telemetridata:

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Lägg till följande hjälpfunktion om du vill skriva ut Åtgärdsresultat:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Lägg till följande hjälpfunktion du använder för att slumpgenerera telemetrivärden:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Lägg till följande definition för det **DeviceInfo** objekt enheten skickar vid start:

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Lägg till följande definition för enhetstvillingen rapporterade värden. Den här definitionen innehåller beskrivningar av de direkta metoder som enheten har stöd för:

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Lägg till följande funktion för att hantera den **omstart** dirigera metodanrop:

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Lägg till följande funktion för att hantera den **InitiateFirmwareUpdate** dirigera metodanrop. Den här direkt metod använder en parameter för att ange platsen för avbildningen för inbyggd programvara för att ladda ned och initierar den uppdatering av inbyggd programvaran på enheten asynkront:

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Lägg till följande kod för att skapa en klientinstans:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Lägg till följande kod:

    * Öppna anslutningen.
    * Skicka den **DeviceInfo** objekt.
    * Ställ in en hanterare för önskade egenskaper.
    * Skicka rapporterade egenskaper.
    * Registrera hanterare för direkta metoder.
    * Börja skicka telemetri.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. Spara ändringarna i den **remote_monitoring.js** fil.

1. Kör följande kommando i Kommandotolken för att starta exempelprogrammet:
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
